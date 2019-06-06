# Logstash JDBC Input Plugin

### 1. Plugin Developement and Testing

#### Code 
- To get started, you'll need JRuby with the Bundler gem installed.

- Install dependencies
```sh
bundle install
```


#### 2. Run in an installed Logstash

- Build your plugin gem
```sh
gem build logstash-input-jdbc.gemspec
```
- Install the plugin from the Logstash home
```sh
bin/plugin install /your/local/plugin/logstash-input-jdbc.gem
```
- Start Logstash and proceed to test the plugin

## Example configuration

Reading data from MySQL:  

	input {
	  jdbc {
	    jdbc_driver_library => "/path/to/mysql-connector-java-5.1.33-bin.jar"
	    jdbc_driver_class => "com.mysql.jdbc.Driver"
	    jdbc_connection_string => "jdbc:mysql://host:port/database"
	    jdbc_user => "user"
	    jdbc_password => "password"
      # or jdbc_password_filepath => "/path/to/my/password_file"
	    # statement => "SELECT ..."
		# 原分页开关
	    # jdbc_paging_enabled => "true"
		# 新增分页优化
		subquery_paging_enabled => "true"
		sum_statement => "select count(*) as sum from goods"
		# 优化后分页SQL
		statement => "select t1.* from goods t1 inner join(select id from goods order by id limit :data_offset , :jdbc_page_size) as lim using(id)"
	    jdbc_page_size => "50000"
	  }
	}

	filter {
	  [some filters here]
	}

	output {
	  stdout {
	    codec => rubydebug
	  }
	  elasticsearch_http {
	    host => "host"
	    index => "myindex"
	  }
	}
