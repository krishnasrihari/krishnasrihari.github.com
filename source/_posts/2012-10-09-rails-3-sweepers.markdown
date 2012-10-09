---
layout: post
title: "Rails 3 sweepers"
date: 2012-10-09 20:14
comments: true
categories: [rails 3 sweeper]
author: Krishna Srihari
---

Rails 3 sweepers are same as observers but there are small changes for configuration

#### Create sweeper Directory
Create separate directory name 'sweepers' for your application in app directory

#### Configuration
Add your all sweepers to autooload in config/application.rb
    config.autoload_paths += %W(#{config.root}/app/sweepers)

#### RSpec test case for sweeper
Write test cases for your sweepers 
  	let(:photo) { FactoryGirl.create(:photo) }
		let(:sweeper) { CommentSweeper.instance}
	
		context "#after save" do
			it "should be called" do
				sweeper.should_receive(:after_save)
				ActiveRecord::Observer.with_observers(:comment_sweeper) do			
					photo.comments.create(:comment =>  "test")
				end			
			end		
		end

Write test case for caching
		context "#after save cache" do
			before do
				Rails.cache.clear
				ActionController::Base.perform_caching = true
				Rails.cache.write("views/photo-#{photo.id}-comments","photo comments content")
			end
			after do
				ActionController::Base.perform_caching = false
			end
			
			it "should be clear cached content " do			
				ActiveRecord::Observer.with_observers(:comment_sweeper) do			
					photo.comments.create(:comment =>  "update photo comments content")
				end
				Rails.cache.read("views/photo-#{photo.id}-comments").should be_nil
			end		
		end


<https://github.com/patmaddox/no-peeping-toms> gem required to pass this testcase

#### Create new sweepers and bind the model to listen
		class CommentSweeper < ActionController::Caching::Sweeper
			observe Comment # Bind to Comment model
			
			def after_save(comment)
				expire_cache_for(comment)
			end
		
			def after_destroy(comment)
				expire_cache_for(comment)
			end
			
			private			
				def expire_cache_for(comment)
					expire_fragment("comment-#{comment.id}")
					Rails.cache.delete("views/photo-#{comment.photo.id}-comments")					
				end
		end

#### Add it to controller
    class CommentsController < ApplicationController
    	cache_sweeper :comment_sweeper, :only => :create
    	
    	def create
    		....
    	end
    end
    
Once comment#create executed your sweeper expires the fragment cache.
    
Execute your sweeper test case, it will execute without any error. 		

