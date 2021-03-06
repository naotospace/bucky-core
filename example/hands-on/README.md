# Hands-on

## What is this example doing
It will show you how to create a project, service, page and some test code using Bucky command.
And will show you how to execute these test code.

## Before starting
Make sure you have already installed gem "bucky-core".
```bash
gem install bucky-core
```

## 1. Create a new test project
* Project directory is your working directory when you are executing Bucky test code

Create a new test project.
```bash
bucky new hands-on
```
You will get a new directory which name is "hands-on" in the current directory.

## 2. Create a new service in the project
* Service means the application name that you are going to test
* You are able to create multiple services in one project
* Use snake case for naming

Move into project directory and create a service.
```bash
cd hands-on
bucky make service bucky_hands_on
```
You will get a new directory which name is "bucky_hands_on" under services directory.

## 3. Create devices directory and create page_object, parts for test code
* Device only support PC, SP and tablet
* Test code will be executed in different user agents according to the device
* Page_object is the file that you can add customize method in
* Parts is the file that HTML element of the page is declared in
* Use snake case for naming

Create device directory, page_object file and part file.
```bash
# Create github top page
bucky make page github_top --service bucky_hands_on --device pc
# Create github search list page
bucky make page github_search_list --service bucky_hands_on --device pc
```
In this time, your directory will look like the following:
```bash
hands-on/
├── README.md
├── config
│   ├── bucky_config.yml
│   ├── e2e_config.yml
│   ├── linkstatus_config.yml
│   └── test_db_config.yml
├── services
│   ├── README.md
│   └── bucky_hands_on
│       └── pc
│           ├── pageobject
│           │   ├── github_search_list.rb
│           │   └── github_top.rb
│           └── parts
│               ├── github_search_list.yml
│               └── github_top.yml
└── system
    ├── evidences
    │   ├── README.md
    │   └── screen_shots
    │       └──  README.md
    └── logs
        └──  README.md
```

## 4. Add the element in part file, which are going to be used in test code
* Part file can be written in xpath or id
* Use snake case for naming

Add the search bar and search result element in github_top.yml.
```
## services/bucky_hands_on/pc/parts/github-top.yml ##
search_bar:
    - xpath
    - //input[contains(@class,'header-search-input')]
search_resault:
    - xpath
    - //div[contains(@class,'jump-to-suggestions')]
```
Add bucky-core click element in github_search_list.yml.
```
## services/bucky_hands_on/pc/parts/github_search_list.yml ##
bucky_core_a_tag:
    - xpath
    - //a[contains(text(),'lifull-dev')]
```

## 5. Create test code in E2E (End-to-End) category
* This step can't be done by Bucky command
* Use snake case for naming

Create e2e category directory.
```bash
mkdir -p services/bucky_hands_on/pc/scenarios/e2e
```
Create test code file, which test suite name is "search_and_assert".
```bash
touch services/bucky_hands_on/pc/scenarios/e2e/search_and_assert.yml
```
Add test code in test suite.
```
## services/bucky_hands_on/pc/scenarios/e2e/search_and_assert.yml ##
# Describe for this test suite
desc: search in github and check page transition
device: pc
service: bucky_hands_on
priority: high
test_category: e2e
labels:
  - example
cases:
  # You should create test case name as {test suite name + _ + number}
  - case_name: search_and_assert_1
    func: transition
    desc: Should able to search bucky-core in github, and move to bucky-core page.
    # Procedures to do in this case
    procs:
      - proc: Open github top page
        exec:
          operate: go
          url: https://github.com/
      - proc: Input 'bucky-core' in search bar
        exec:
          operate: input
          page: github_top
          part: search_bar
          word: 'bucky-core'
      - proc: Click search result
        exec:
          operate: click
          page: github_top
          part: search_resault
      - proc: Click target result
        exec:
          operate: click
          page: github_search_list
          part: bucky_core_a_tag
      - proc: assert_text
        exec:
          verify: assert_title
          expect: 'GitHub - lifull-dev/bucky-core: System testing framework for web application.'
```
## 6. Create test code in Linkstatus category
* This step can't be done by Bucky command
* Use snake case for naming

Create linkstatus category directory.
```bash
mkdir -p services/bucky_hands_on/pc/scenarios/linkstatus
```
Create test code file, which test suite name is "github_top".
```bash
touch services/bucky_hands_on/pc/scenarios/linkstatus/github_top.yml
```
Add test code in test suite.
```
## services/bucky_hands_on/pc/scenarios/linkstatus/github_top.yml ##
# Describe for this test suite
desc: Check all a tag in githib top
device: pc
service: bucky_hands_on
priority: high
test_category: linkstatus
labels:
  - example
# You can exclude url that you don't want to check
exclude_urls:
  - https://github.com/customer-stories/*
cases:
  - case_name: github_top_1
    desc: Check github top
    urls:
      - https://github.com/
```

## 7. Execute test code
* You should start Selenium Chrome driver first

You can find how to start Selenium Chrome driver by Docker in [SeleniumHQ/docker-selenium](https://github.com/SeleniumHQ/docker-selenium).

Set the connection for E2E test.
```
## config/e2e_config.yml ##
:selenium_ip: {your ip for selenium}
:selenium_port: '4444'
```

Execute the test by Bucky command. You should execute the test code in working directory. In this case, the working directory is: "hands-on/".
```bash
# Use -d option, because we doesn't start-up bucky-managemnt in this example.
bucky run -t e2e -c search_and_assert_1 -d
bucky run -t linkstatus -c github_top_1 -d
```

You will get resault like the following:
```
bucky run -t e2e -c search_and_assert_1 -d

Loaded suite /usr/local/bin/bucky
Started

search_and_assert_1(TestBuckyHandsOnPcE2eSearchAndassert)
 Sholud able to serch bucky-core in github, and transition to it. ....
  1:Open github top page
    {:operate=>"go", :url=>"https://github.com/"}
  2:Input 'bucky-core' in search bar
    {:operate=>"input", :page=>"github_top", :part=>"search_bar", :word=>"bucky-core"}
  3:Click search result
    {:operate=>"click", :page=>"github_top", :part=>"search_resault"}
  4:Click target result
    {:operate=>"click", :page=>"github_search_list", :part=>"bucky_core_a_tag"}
  5:assert_text
    assert_title is defined in E2eVerificationClass.
    {:verify=>"assert_title", :expect=>"GitHub - lifull-dev/bucky-core: Bucky is a testing framework that supports Web System Testing Life Cycle."}
.

Finished in 8.948734 seconds.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
1 tests, 5 assertions, 0 failures, 0 errors, 0 pendings, 0 omissions, 0 notifications
100% passed
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
0.11 tests/s, 0.56 assertions/s



bucky run -t linkstatus -c github_top_1 -d

Loaded suite /usr/local/bin/bucky
Started

github_top_1(TestBuckyHandsOnPcLinkstatusGithubTop))}
  https://github.com/ ...  [200:OK]
  https://github.com/ is already [200:OK]
  https://github.com/features/actions ...  [200:OK]
  https://github.com/features#team-management ...  [200:OK]
  https://github.com/join?source=header-home ...  [200:OK]
  https://github.com#start-of-content ...  [200:OK]
  https://github.com/features/code-review/ ...  [200:OK]
  https://github.com/features ...  [200:OK]
  https://github.com/features/project-management/ ...  [200:OK]
  https://github.com/features#code-hosting ...  [200:OK]
  https://github.com/features#social-coding ...  [200:OK]
  https://github.com/customer-stories ...  [200:OK]
  https://github.com/security ...  [200:OK]
  https://github.com/collections ...  [200:OK]
  https://github.com/topics ...  [200:OK]
  https://github.com/features#documentation ...  [200:OK]
  https://github.com/events ...  [200:OK]
  https://github.com/enterprise ...  [200:OK]
  https://github.com/trending ...  [200:OK]
  https://github.com/pricing#feature-comparison ...  [200:OK]
  https://github.com/explore ...  [200:OK]
  https://github.com/nonprofit ...  [200:OK]
  https://github.com/marketplace ...  [200:OK]
  https://github.com/pricing ...  [200:OK]
  https://github.com/login ...  [200:OK]
  https://github.com/features/integrations ...  [200:OK]
  https://github.com ...  [200:OK]
  https://github.com/business ... redirect to https://github.com/enterprise [302:RD]
  https://github.com/open-source ...  [200:OK]
  https://github.com/enterprise is already [200:OK]
  https://github.com/join?source=button-home ...  [200:OK]
  https://github.com/join?plan=business&setup_organization=true&source=business-page ...  [200:OK]
  https://github.com/personal ...  [200:OK]
  https://github.com/about/careers ...  [200:OK]
  https://github.com/contact ...  [200:OK]
  https://github.com/about ...  [200:OK]
  https://github.com/features/project-management ...  [200:OK]
  https://github.com/site/terms ... redirect to https://help.github.com/articles/github-terms-of-service/ [301:RD]
  https://github.com/features/code-review ...  [200:OK]
  https://github.com/about/press ...  [200:OK]
  https://github.com/site/privacy ... redirect to https://help.github.com/articles/github-privacy-statement/ [302:RD]
  https://github.com/github ...  [200:OK]
  https://help.github.com/articles/github-terms-of-service/ ... redirect to https://help.github.com/articles/github-terms-of-service [301:RD]
  https://help.github.com/articles/github-privacy-statement/ ... redirect to https://help.github.com/articles/github-privacy-statement [301:RD]
  https://help.github.com/articles/github-terms-of-service ... redirect to https://help.github.com/en/articles/github-terms-of-service [301:RD]
  https://help.github.com/articles/github-privacy-statement ... redirect to https://help.github.com/en/articles/github-privacy-statement [301:RD]
  https://help.github.com/en/articles/github-privacy-statement ...  [200:OK]
  https://help.github.com/en/articles/github-terms-of-service ...  [200:OK]
.

Finished in 11.283795 seconds.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
1 tests, 2 assertions, 0 failures, 0 errors, 0 pendings, 0 omissions, 0 notifications
100% passed
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
0.09 tests/s, 0.18 assertions/s
```

## At last
Congratulations!! You have just finish this example.

If you follow these procedures your directory will be like this:
```
hands-on/
├── README.md
├── config
│   ├── bucky_config.yml
│   ├── e2e_config.yml
│   ├── linkstatus_config.yml
│   └── test_db_config.yml
├── services
│   ├── README.md
│   └── bucky_hands_on
│       └── pc
│           ├── pageobject
│           │   ├── github_search_list.rb
│           │   └── github_top.rb
│           ├── parts
│           │   ├── github_search_list.yml
│           │   └── github_top.yml
│           └── scenarios
│               ├── e2e
│               │   └── search_and_assert.yml
│               └── linkstatus
│                   └── github_top.yml
└── system
    ├── evidences
    │   ├── README.md
    │   └── screen_shots
    │       └──  README.md
    └── logs
        └──  README.md
```

We have learned how to create project, service, page, and test code. And also learned how to execute test code using Bucky command.

Now you can create you own test code!!
