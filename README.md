# Friendly-Cukes
friendly-cukes is user friendly cucumber test automation framework created by tester for tester.
It is a ruby-cucumber based generic automation framework for desktop and mobile web automation. It is built using Cucumber (1.3.15), page-object (1.0.2) and appium_lib (4.1.0). All you need is to install the gem and that's it, you have set with cucumber automation.

#### Gem requirements
The gem requires [Ruby (2.0.0)](https://www.ruby-lang.org/en/downloads/), [ChromeDriver.exe (v2.10.267521)](http://chromedriver.storage.googleapis.com/index.html), [IEDriverServer.exe (2.45.0.0)](http://selenium-release.storage.googleapis.com/index.html) and [Appium (1.3.4.1)](http://appium.io/downloads.html). As of now the framework is designed to work with the proposed software setup and tested only on Windows machine.

1. Install friendly-cukes gem

		gem install friendly-cukes

2. Install generic cucumber framework 

		friendly-cukes --new test_app_name
	where `test_app_name` is the test application name
	This will install the generic cukes framework under `test_app_name` directory (type `friendly-cukes` for more options)
	
3. Then type `cd test_app_name` and start with the cucumber automation.
	By default the framework has some feature files for demo purpose.
	The cucumber run command for the default feature files is created as rake task, Type `rake -T` and it will list three tasks. Run any of the task, like `rake desktop_google_search` and it will start the cucumber execution

4. The cucumber run command will look like
	
		$cucumber -p profile_name --tags tag1[,tag2,...] APP=app_name
	
	where 
	* `profile_name` - is the cucumber profile name present in `cucumber.yml`
	* `tags` - is the tag name for feature/scenario to be executed
	* `app_name` - key name of the application (as of now I have used two different app;Google and Rubygems for demo purpose)
	
	Example:
	
		$cucumber -p desktop_dev_ff -t @tc_02 APP=RUBYGEMS
		
	the above command runs scenario tag `@tc_02` (from `gem_search.feature`) with `desktop_dev_ff profile` (from `cucumber.yml`)
	
5. For releasing unused profiles add `RESET_CONFIG_VALUES=yes` or `true` in the cucumber run command and it will release all the no-longer used profiles

6. For adding code coverage report, add `CODE_COVERAGE=yes` or `true` in cucumber run command and it will create a `coverage` directory holding the code-coverage report files

7. For mobile automation, use the profile (in `cucumber.yml`) prefixed with `mobile_`

	Example:
	
		$cucumber -p mobile_dev_chrome_device -t @tc_02 APP=RUBYGEMS
		
	the above command will execute scenario with tag `@tc_02` in chrome in the device attached to the machine, provided the android and appium settings are correct and the `appium server` is up and running

# TAF Features
## Object Repository
1. all web page-objects (web elements) are grouped into modules and are placed under `object_repository` directory
	
2. the `page-object` repository modules can be encapsulated with any `PageObject` class files
	
3. there can be multiple repository files
	
4. the `page_objects` directory holds generic page object class methods that defines the actual script action (with the objects from `object_repository`) on web browser

## Test data handling
YML files are used as test data files for faster data access and these files are stored under `test_data` directory under feature file directory. The test data handling is done at two levels
	
1. local : 
	the scenario specific test data file should be placed under the respective feature file directory and with the file name suffixed with the scenario ID
			
2. global :
	the global test data files should be placed under test_data directory under the root feature (`desktop` or `mobile`) directory and if no local test data file is present, then it takes the global data file as default

## Dynamic profile and Parallel execution

###	1. Dynamic profiles
* the profile allocation for each execution ( on single or multiple machines) is done dynamically during the execution
		
* based on the profile tags given in `cucumber.yml`, the free profiles are auto-assigned to the currently running execution
		
* the profiles are configured in such a way that there can be `n` nodes and each node can have `m` profiles and hence the profile sample space is `n x m`

###	2. Parallel execution	
* the parallel execution parameters are given in `config.yml` based on which the merging of custom html report files will be done
		
* the profiles for each parallel execution is taken from `config.yml` dynamically

## Custom Execution report

for each execution an unique test report directory will be created with the name `test_report_<timestamp>` under which by default the following exists
		
* `app_env.log` –> holds the log for execution environment and duration details
		
* `feature based custom log file` –> holds the log based on the currently running feature – there can `n` number of feature log files
		
* `report_<timestamp>.html` –> cucumber generated html report file that holds the log for features, scenarios and steps
		
* `report_<timestamp>.json` –> cucumber generated json report file that holds the log for features, scenarios and steps and the step level durations in nano seconds
		
* `custom_report` –> the directory holds merged custom html report files
	
* `report_home.html` –> holds the summary report of the execution

## Performance Report

* the performance report component extracts the build details from cucumber generated json report files and stores in into `Syabse` database
		
* the complete execution data for each build will be stored in the DB, which can be interpreted and populated as a performance report
		
* the performance report can included or excluded based on the property value of performance_report in `config.yml`
		
* this component is also available as a standalone application bundled with the `friendly-cukes` gem, type `build-extractor` and follow the commands
		
* this will work only if `Sybase` database is configured in TAF and tables are created with TAF defined relationships
	
