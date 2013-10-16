# Logfile Transfer

Ruby monitoring and transform logfiles daemon.

## Installation

    gem install filemonitor
    # you may need to run:
    sudo gem install logfile_transfer

## Examples

### Edit test.rb

    require 'logfile_transfer'

    class Test < LogfileTransfer::Handler

      def init
      end

      def handle log_path, log_fn, line, line_count, pattern
        puts "#{log_path}, #{log_fn}, #{line_count}, #{pattern}"
      end

    end

    # The daemon binding on port 2001, make it different in other daemon
    LogfileTransfer.run ARGV, 2001, File.expand_path(File.dirname(__FILE__))

### Edit config.yaml

    ---
    - !ruby/object:LogfileTransfer::FileMonitorObj
      absolute_path: /data/webroot/log
      dir_disallow: []
      file_disallow:
      - .*
      file_allow:
      - \.log\.
      patterns:
      - - .*
        - - !ruby/object:Test {}

## Run

    # as root user, run:
    Ruby test.rb start
    # you may need to run:
    sudo Ruby test.rb start

## Status

    Ruby test.rb status

## Stop

    Ruby test.rb stop

## Multiple folders, different log and more Handler in a daemon process

### Edit test.rb

    require 'logfile_transfer'

    class Test < LogfileTransfer::Handler

      def init
      end

      def handle log_path, log_fn, line, line_count, pattern
        puts "#{log_path}, #{log_fn}, #{line_count}, #{pattern}"
      end

    end

    class Test1 < LogfileTransfer::Handler

      def init
      end

      def handle log_path, log_fn, line, line_count, pattern
        if (line_count % 2) == 0
          puts '+++++++++++++++++++'
        else
          puts '-------------------'
        end
      end

    end

    LogfileTransfer.run ARGV, 2001, File.expand_path(File.dirname(__FILE__))

### Edit config.yaml

    ---
    - !ruby/object:LogfileTransfer::FileMonitorObj
      absolute_path: /data/webroot/log
      dir_disallow: []
      file_disallow:
      - .*
      file_allow:
      - \.log\.
      patterns:
      - - gamestart
        - - !ruby/object:Test {}
          - !ruby/object:Test1 {}
      - - gamestop
        - - !ruby/object:Test {}
          - !ruby/object:Test1 {}
    - !ruby/object:LogfileTransfer::FileMonitorObj
      absolute_path: /data/webroot/tlog
      dir_disallow: []
      file_disallow:
      - .*
      file_allow:
      - \.log\.
      patterns:
      - - gamestart
        - - !ruby/object:Test {}
          - !ruby/object:Test1 {}
      - - gamestop
        - - !ruby/object:Test {}
          - !ruby/object:Test1 {}
