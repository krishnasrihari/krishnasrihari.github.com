---
layout: post
title: "Rails 3 sweepers"
date: 2012-10-09 13:34
comments: true
categories: 
---

Rails 3 sweepers are same as observers but there are small changes for configuration

#### Create sweeper Directory
Create separate directory name 'sweepers' for your application in app directory

#### Configuration
Add your all sweepers to autooload in config/application.rb
    config.autoload_paths += %W(#{config.root}/app/sweepers)

#### RSpec test case for sweeper
		require 'spec_helper'
		
		describe CommentSweeper do
			context "#after create" do
				it "should be called" do
					photo = FactoryGirl.create(:photo)
					
					sweeper = CommentSweeper.instance
					sweeper.should_receive(:after_save)
					ActiveRecord::Observer.with_observers(:comment_sweeper) do			
						photo.comments.create(:comment =>  "test")
					end			
				end
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

