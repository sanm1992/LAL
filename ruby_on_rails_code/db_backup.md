```ruby
# lib/tasks/db:backups.rake
# example rake db:backup:create RAILS_ENV = development
namespace :db do  
    namespace :backup do  
        RAILS_ENV = Rails.env
        def makeup_backup_folder  
            backup_folder = File.join(ENV["DIR"] || "db", 'backup')  
            FileUtils.mkdir_p(backup_folder)  
            backup_folder  
        end  
  
        def makeup_backup_filename(magic_date)  
            "#{RAILS_ENV}#{magic_date.strftime("%Y%m%d%H%M%S")}.sql"  
        end  
          
        desc "Backup the database to a file. Options: DIR=base_dir RAILS_ENV=production DATE=2008-10-11"  
        task :create => [:environment] do  
            magic_date = (Date.parse(ENV["DATE"]) if ENV["DATE"]) || Time.now  
            backup_folder = makeup_backup_folder  
            backup_file = File.join(backup_folder, makeup_backup_filename(magic_date))  
            db_config = ActiveRecord::Base.configurations[RAILS_ENV]  
            system "mysqldump -u #{db_config['username']} #{'-p' if db_config['password']}#{db_config['password']} --opt #{db_config['database']} > #{backup_file}"  
              
                        file_counter = Dir.glob("#{backup_folder}/*.sql").size  
            puts "Created backup: #{backup_file}"  
            puts "#{file_counter} backups available"  
        end  
      
        desc "Remove all the batabase backup files which had been created before a certain date, default is today. Options: DIR=base_dir RAILS_ENV=production DATE=2008-10-11"  
        task :destroy => [:environment] do  
            magic_date = (Date.parse(ENV["DATE"]) if ENV["DATE"]) || Time.now.to_date  
            backup_folder = makeup_backup_folder  
            magic_name = makeup_backup_filename(magic_date)  
  
            dir = Dir.new(backup_folder)  
            (dir.entries - ['.', '..']).each do |backup|  
                if backup.starts_with?(RAILS_ENV) && backup < magic_name  
                    backup_file = File.join(backup_folder, backup)  
                    FileUtils.rm_rf(backup_file)  
                    puts "Removed backup: #{backup_file}"  
                end  
            end  
        end  
          
        desc "Rebuild the database with a certain back file. Options: DIR=basedir RAILS_ENV=production"  
        task :rebuild => [:environment] do  
            raise 'only for development or test environment' unless ["development", "test"].include? RAILS_ENV  
            backup_folder = makeup_backup_folder  
            db_config = ActiveRecord::Base.configurations[RAILS_ENV]  
            Rake::Task["db:drop"].invoke  
            Rake::Task["db:create"].invoke  
              
            bakfile = ENV['FILE']  
            (Dir.new(backup_folder).entries - ['.', '..']).sort.reverse.each do |backup|  
              (bakfile = backup and break) if backup.starts_with?(RAILS_ENV)  
            end unless bakfile  
            raise 'could not find the backup file!' unless bakfile  
              
            ActiveRecord::Base.establish_connection(RAILS_ENV)  
            ActiveRecord::Base.connection.execute('SET foreign_key_checks = 0')  
            puts "rebuild database #{db_config['database']} from #{bakfile}"  
              
            system "mysql -u #{db_config['username']} #{'-p' if db_config['password']}#{db_config['password']} #{db_config['database']} < #{makeup_backup_folder}/#{bakfile}"  
        end  
    end  
end  
```
