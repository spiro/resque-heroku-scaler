Resque Heroku Scaler
====================

Provides autoscaling for [Resque][rq] workers on [Heroku][hk]. Based on
previous scaling work developed by [Daniel Huckstep][dh] and
[Alexander Murmann][am].

Autoscaling behavior is provided through a separate monitor process. The
scaler monitor process polls for pending jobs against the specified Resque
Redis backend at a configurable interval. The scaler process runs as a worker
process on Heroku.

##Setup

Add the following environment variables to your Heroku environment:

* HEROKU_APP
* HEROKU_USERNAME
* HEROKU_PASSWORD

In your Procfile, configure the scaler as a worker process using:

scaler: bundle exec rake resque:scaler:run

To run the scaler process, use the following command. Note, the scaler process
is intended to run as a single instance.

heroku scale scaler=1

In your development environment, the scaler process can run local worker
processes using the rush library. To configure, use the following in
an initializer.

require 'resque/plugins/resque-heroku-scaler'

if Rails.env.development?
  ENV["RUSH_PATH"] ||= File.expand_path('/path/to/app', __FILE__)
  Resque::Plugins::ResqueHerokuScaler.configure do |c|
    c.scale_manager = :local
  end
end

[rq]: http://github.com/defunkt/resque
[hk]: http://devcenter.heroku.com/articles/cedar
[dh]: http://verboselogging.com/2010/07/30/auto-scale-your-resque-workers-on-heroku
[am]: https://github.com/ajmurmann/resque-heroku-autoscaler