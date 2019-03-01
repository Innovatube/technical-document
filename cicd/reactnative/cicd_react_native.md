### Các công cụ cần tìm hiểu 
- [AppCenter](https://docs.microsoft.com/en-us/appcenter/)
- [Fastlane](https://docs.fastlane.tools/)
- [Jenkin](https://jenkins.io/doc/)

### 1: AppCenter

- Trong sự kiện Connect()- 2016, Microsoft giới thiệu Visual Studio Mobile Center, một giải pháp có thể nói là all-in-one trong phát triển ứng dụng di động 
- Định nghĩa all-in-one trong lập trình ứng dụng di động có nghĩa là một giải pháp giúp hỗ trợ đầy đủ hoặc gần như đầy đủ các công việc quan trọng trong việc phát triển ứng dụng di động, từ quá trình build cho tới quá trình test, phân phối (distribution) và theo dõi, phân tích hậu phân phối (analytics)
- Các tính năng của AppCenter thì đã rất rõ ràng, nhưng có một thực tế khi dùng AppCenter là nó build khá lâu, Team Frontend đã dùng AppCenter cho giai đoạn đầu của dự án ShouboTenken, thời gian build có khi lên đến 20-30 phút. Chính vì thế Team Frontend đã tìm một giải pháp build mới nhanh hơn đó chính là Fastlane, nhưng vẫn sử dụng AppCenter cho quá trình distribution ứng dụng 
### 2: Fastlane 
### 2.1: Định nghĩa 

    - Fastlane là một công cụ giúp cho việc release sản phẩm của chúng ta trở nên dễ dàng hơn, nhanh hơn. Nó xử lý tất cả các công việc dườm dà như tạo screenshot, xử lý code signing và release ứng dụng lên Store 
### 2.2: Ta có thể làm gì với Fastlane 

    - Xác định rõ việc deploy sản phẩm, bản beta hay testing 
    - Dễ dàng setup trong vài phút
    - Không cần nhớ các câu lệnh dài dòng, phức tạp chỉ cần nhớ fastlane là đủ 
    - Lưu trữ mọi thứ trên Git, dễ config 
    - Hỗ trợ cho cả Android, IOS, MacOS
    ...
### 2.3: Cài đặt
### 2.3.1: Update Xcode command line tool

 Trước khi cài đặt fastlane, bạn phải đảm bảo rằng đã cài Xcode command line tools version mới nhất 
```sh
xcode-select --install
```
### 2.3.2: Các tool đi kèm

Để sử dụng fastlane, chúng ta phải cài đặt thêm một số tool đi kèm như :
### [match](https://medium.com/@danielvivek2006/setup-fastlane-match-for-ios-6260758a9a4e)
* Match là một phương pháp mới cho IOS code siging, chia sẻ code siging cho các thành viên của team để đơn giản hóa việc cài đặt codesiging
* Match tạo tất cả các certificates và provisioning profile, lưu chúng trên mỗi git repository riêng biệt. Các thành viên của team có quyền truy cập Repo này có thể sử dụng chúng cho code signing.
* Cài đặt: 
```sh
brew cask install match
```
### [gym](https://github.com/fastlane/fastlane/tree/master/gym)

* Gym là môt công cụ để build and packages IOS app
* Cài đặt: 
```sh
brew cask install gym
```
### [pilot](https://github.com/fastlane/fastlane/tree/master/pilot)

* Tự động deploy app lên TestFlight và quản lý beta tester
* Nhưng trong document này, Team Frontend không sử dụng TestFlight mà distribute app thông qua  AppCenter()
* Cài đặt: 
```sh
brew cask install pilot
```
### 2.3.3: Cài đặt fastlane 
`Sử dụng Homebrew`
```sh
brew cask install fastlane
```
`Hoặc sử dụng RubyGems`
```sh
sudo gem install fastlane -NV
```


### 2.4: Sử dụng Fastlane
* Đầu tiên tạo Folder fastlane/ trong project react-native ở root level, sau đó tạo 1 file gọi là Fastfile trong folder fastlane/
* Fastfile là nơi thực hiện các lanes. Mỗi lane là một tập hợp các action được thực thi đồng bộ. Mỗi action, là một tập hợp các function mà thực thi một task nào đó 
```
  before_all do
    ensure_git_branch
    ensure_git_status_clean
    git_pull
  end

  platform :ios do
    # iOS Lanes
  end

  platform :android do
    # Android Lanes
  end
```
* Chúng ta define 2 nền tảng Android và IOS, trong mỗi nền tảng chứa các lane đặc biệt cho mỗi context.
* Để thực thi các lane ta gọi các câu lênh 
```sh
fastlane ios lane or fastlane android lane
```

### 2.5: Sử dụng Fastlane qua Project thực tế: ShouboTenken

### Fastfile
```
  # update_fastlane

default_platform(:ios)

AC_API_TOKEN = "**************************"
AC_OWNER_NAME = "**********"
AC_APP_NAME_IOS = ""**********""
AC_APP_NAME_ANDROID = ""**********""
AC_TEAM_INTERNAL = "Internal"
AC_TEAM_STAGING = "Staging"
AC_TEAM_CUSTOMER = "Customer"

platform :ios do
  desc "Description of what the lane does"
  lane :device do
    register_devices(
      devices_file: "fastlane/device.txt",
      team_id: "************",
      username: "*******@gmail.com"
    )
  end

  desc "Description of what the lane does"
  lane :build_dev do
    sh "cp", "-rf", "../.env_development", "../.env"
    increment_build_number(
      build_number: Time.now.getutc.to_i,
      xcodeproj: "ios/*********.xcodeproj"
    )
    gym(
      project: "ios/*********.xcodeproj",
      scheme: "*********",
      export_method: "ad-hoc",
      configuration: "Release",
      silent: true,
      suppress_xcode_output: true,
      clean: true,
      output_directory: "build",
      output_name: "ShouboTenken.ipa",
      export_options: {
        iCloudContainerEnvironment: "Development",
        provisioningProfiles: {
          "com.*****.*****": "match AdHoc com.*****.*****"
        }
      }
    )
    appcenter(
      api_token: AC_API_TOKEN,
      owner_name: AC_OWNER_NAME,
      app_name: AC_APP_NAME_IOS,
      testing_group: AC_TEAM_INTERNAL,
      release_notes: "[Develop] Commit #{last_git_commit()[:commit_hash]} of #{last_git_commit()[:author]}",
      file_path: "build/ShouboTenken.ipa"
    )
  end

  desc "Description of what the lane does"
  lane :build_staging do
    sh "cp", "-rf", "../.env_staging", "../.env"
    increment_build_number(
      build_number: Time.now.getutc.to_i,
      xcodeproj: "ios/*********.xcodeproj"
    )
    gym(
      project: "ios/*********.xcodeproj",
      scheme: "shoubotenken",
      export_method: "ad-hoc",
      configuration: "Release",
      silent: true,
      suppress_xcode_output: true,
      clean: true,
      output_directory: "build",
      output_name: "ShouboTenken.ipa",
      export_options: {
        iCloudContainerEnvironment: "Development",
        provisioningProfiles: {
          "com.*****.*****": "match AdHoc com.*****.*****"
        }
      }
    )
    appcenter(
      api_token: AC_API_TOKEN,
      owner_name: AC_OWNER_NAME,
      app_name: AC_APP_NAME_IOS,
      testing_group: AC_TEAM_STAGING,
      release_notes: "[Staging] Commit #{last_git_commit()[:commit_hash]} of #{last_git_commit()[:author]}",
      file_path: "build/ShouboTenken.ipa"
    )
  end

  desc "Description of what the lane does"
  lane :build_staging_customer do
    sh "cp", "-rf", "../.env_staging", "../.env"
    increment_build_number(
      build_number: Time.now.getutc.to_i,
      xcodeproj: "ios/*********.xcodeproj"
    )
    gym(
      project: "ios/*********.xcodeproj",
      scheme: "shoubotenken",
      export_method: "ad-hoc",
      configuration: "Release",
      silent: true,
      suppress_xcode_output: true,
      clean: true,
      output_directory: "build",
      output_name: "ShouboTenken.ipa",
      export_options: {
        iCloudContainerEnvironment: "Development",
        provisioningProfiles: {
          "com.*****.*****": "match AdHoc com.*****.*****"
        }
      }
    )
    appcenter(
      api_token: AC_API_TOKEN,
      owner_name: AC_OWNER_NAME,
      app_name: AC_APP_NAME_IOS,
      testing_group: AC_TEAM_CUSTOMER,
      release_notes: "[Staging] Commit #{last_git_commit()[:commit_hash]} of #{last_git_commit()[:author]}",
      file_path: "build/ShouboTenken.ipa"
    )
  end
end

platform :android do
  desc "Build development"
  lane :build_dev do
    sh "cp", "-rf", "../.env_development", "../.env"
    Dir.chdir("../android") do
      sh "./gradlew assembleRelease -PversionCode=#{Time.now.getutc.to_i}"
    end

    appcenter(
      api_token: AC_API_TOKEN,
      owner_name: AC_OWNER_NAME,
      app_name: AC_APP_NAME_ANDROID,
      testing_group: AC_TEAM_INTERNAL,
      release_notes: "[Develop] Commit #{last_git_commit()[:commit_hash]} of #{last_git_commit()[:author]}",
      file_path: "android/app/build/outputs/apk/release/app-release.apk"
    )
  end

  desc "Build staging"
  lane :build_staging do
    sh "cp", "-rf", "../.env_staging", "../.env"
    Dir.chdir("../android") do
      sh "./gradlew assembleRelease -PversionCode=#{Time.now.getutc.to_i}"
    end
    appcenter(
      api_token: AC_API_TOKEN,
      owner_name: AC_OWNER_NAME,
      app_name: AC_APP_NAME_ANDROID,
      testing_group: AC_TEAM_STAGING,
      release_notes: "[Staging] Commit #{last_git_commit()[:commit_hash]} of #{last_git_commit()[:author]}",
      file_path: "android/app/build/outputs/apk/release/app-release.apk"
    )
  end


  desc "Build staging"
  lane :build_staging_customer do
    sh "cp", "-rf", "../.env_staging", "../.env"
    Dir.chdir("../android") do
      sh "./gradlew assembleRelease -PversionCode=#{Time.now.getutc.to_i}"
    end
    appcenter(
      api_token: AC_API_TOKEN,
      owner_name: AC_OWNER_NAME,
      app_name: AC_APP_NAME_ANDROID,
      testing_group: AC_TEAM_CUSTOMER,
      release_notes: "[Staging] Commit #{last_git_commit()[:commit_hash]} of #{last_git_commit()[:author]}",
      file_path: "android/app/build/outputs/apk/release/app-release.apk"
    )
  end
end

```
* Trên đầu là thông tin về AppCenter, chúng ta dùng để distribute app lên AppCenter sau khi build xong
```
AC_API_TOKEN = "************************"
AC_OWNER_NAME = "***************"
AC_APP_NAME_IOS = "***************"
AC_APP_NAME_ANDROID = "***************"
AC_TEAM_INTERNAL = "Internal"
AC_TEAM_STAGING = "Staging"
AC_TEAM_CUSTOMER = "Customer"
```
[How to get AC_API_TOKEN](https://docs.microsoft.com/en-us/appcenter/api-docs/)
* Tiếp đến trong phần build cho IOS, register devices. Với tài khoản developer cá nhân thì cần đăng ký uuid những device muốn cài đặt thì mới có thể chạy được app( Hoặc có thể dùng tài khoản [enterprice]('https://developer.apple.com/programs/enterprise/') )
Tạo 1 file device.txt và add uuid device muốn test vào
```
Device ID	Device Name
********************	Nexus iPhone 7
********************	Nexus iPhone 8
********************	Nexus iPhone 5
********************	Nexus iPhone 6s plus
```
* Tiếp đến lane build_dev.
+ Đầu tiên copy file .env_development vào file .env (file .env là file chứa thông tin các biến môi trương như url, secret_key..):
``` 
sh "cp", "-rf", "../.env_staging", "../.env"
```
+ Tiếp tăng build_number:
``` 
increment_build_number(
      build_number: Time.now.getutc.to_i,
      xcodeproj: "ios/*********.xcodeproj"
)
```
+ Sau đó dùng gym build với các thông số đầu vào:
``` 
gym(
      project: "ios/*********.xcodeproj",
      scheme: "shoubotenken",
      export_method: "ad-hoc",
      configuration: "Release",
      silent: true,
      suppress_xcode_output: true,
      clean: true,
      output_directory: "build",
      output_name: "ShouboTenken.ipa",
      export_options: {
        iCloudContainerEnvironment: "Development",
        provisioningProfiles: {
          "com.****.****": "match AdHoc com.****.****"
        }
      }
    )
```
+ Tiếp đến là distribute file ipa lên AppCenter:
```
appcenter(
      api_token: AC_API_TOKEN,
      owner_name: AC_OWNER_NAME,
      app_name: AC_APP_NAME_IOS,
      testing_group: AC_TEAM_STAGING,
      release_notes: "[Staging] Commit #{last_git_commit()[:commit_hash]} of #{last_git_commit()[:author]}",
      file_path: "build/ShouboTenken.ipa"
    )
```
+ Về cơ bản lane của Android cũng tương tự bên IOS, chỉ là không cần đăng ký device.
+ Đầu tiên là build:
```
 Dir.chdir("../android") do
      sh "./gradlew assembleRelease -PversionCode=#{Time.now.getutc.to_i}"
    end 
```
+ Sau là đẩy lên AppCenter:
```
appcenter(
      api_token: AC_API_TOKEN,
      owner_name: AC_OWNER_NAME,
      app_name: AC_APP_NAME_ANDROID,
      testing_group: AC_TEAM_STAGING,
      release_notes: "[Staging] Commit #{last_git_commit()[:commit_hash]} of #{last_git_commit()[:author]}",
      file_path: "android/app/build/outputs/apk/release/app-release.apk"
    ) 
```
### 3: Jenkin

### 3.1: Các khái niệm

### CI/CD 
- CI: Continuous Integration, là tích hợp liên tục các source code của các members trong team lại một cách nhanh chóng,giúp kiểm soát được tình hình phát triển thông qua các bước kiểm thử  (Integration test, regession test), nhằm đưa sản phẩm đạt đến sự ổn định.
- CD: Continuous Delivery, được hiểu là chuyển giao liên tục, là một tập hợp các kỹ thuật nhằm đảm bảo việc triển khai tích hợp source code lên môi trường staging. Theo cách này ta có thể đảm bảo source code được kiểm thử một cách tỉ mỉ trươc khi deploy lên môi trường production

### Jenkin
- Jenkins là 1 open source continuous integration tool được viết trên Java. Dự án được forked từ Hudson sau khi xảy ra tranh chấp với Orcale.
- Jenkins cung cấp contious intergration service cho phát triển phần mềm. Nó là hệ thống chạy trên sevlet container như Apache Tomcat
- Jenkins giúp giám sát thực hiện việc thực thi các jobs như build project hay chạy các jobs

### [Jenkin Pipeline](https://jenkins.io/doc/book/pipeline/)
- Là một bộ plugin hỗ trợ việc triển khai và tích hợp continuous delivery  pipelines vào Jenkins
### 3.1: [Cài đặt](https://jenkins.io/download/)
### 3.1: Sử dụng Jenkin trong CI/CD
- Tạo file Jenkinsfile trong folder project
``` 
@Library([
    'slack-message',
    'elastic-beanstalk'
]) _


pipeline {
    agent any
    environment {
        TER_SLACK_HOOK_KEY = '*************************'
        TER_SLACK_CHANNEL = '#****************'
    }
    stages {
        stage('Inform start') {
            steps {
                script {
                    terSlack.message(
                        message: "********** Build Started",
                        color: "warning",
                        fields: [
                            [
                                title: "Branch",
                                value: env.BRANCH_NAME,
                                short: true
                            ]
                        ]
                    )
                }
                sh "yarn install"
            }
        }
        stage('Build develop') {
            when {
                branch "develop"
            }
            steps {
                sh "fastlane ios build_dev"
                sh "fastlane android build_dev"
            }
        }
        stage('Build staging') {
            when {
                branch "release"
            }
            steps {
                sh "fastlane ios build_staging"
                sh "fastlane android build_staging"
            }
        }
    }
    post {
        success {
            script {
                terSlack.message(
                    message: "************ Build success",
                    color: "good",
                    fields: [
                        [
                            title: "Branch",
                            value: env.BRANCH_NAME,
                            short: true
                        ]
                    ]
                )
            }
        }
        failure {
            script {
                terSlack.message(
                    message: "************** Build failed",
                    color: "danger",
                    fields: [
                        [
                            title: "Branch",
                            value: env.BRANCH_NAME,
                            short: true
                        ]
                    ]
                )
            }
        }
        aborted {
            script {
                terSlack.message(
                    message: "********** Build aborted",
                    color: "danger",
                    fields: [
                        [
                            title: "Branch",
                            value: env.BRANCH_NAME,
                            short: true
                        ]
                    ]
                )
            }
        }
    }
}
```
- @Library khai báo việc import library vào trong jenkins.
- agent : chỉ định môi trường sẽ thực thi các thao tác trong steps
- environment: được khai báo trong block pipeline hoặc stage, là 1 chuỗi các cặp Key-Val định nghĩa các biến môi trường cho toàn bộ (global - nếu để tại scope pipeline) hoặc cho 1 stage riêng ( nếu được khai báo trong block stage đấy ). Có hàm credentials() để lấy thông tin xác thực nhạy cảm (biến định nghĩa ra sẽ ko thể xem được raw text của nó mà chỉ thấy được **** nhưng có thể sử dụng để xác thực được. Ở đây mình khai báo các biến cho cả slack library, aws key và gitlab.
- stages: Mình khai báo 1 stage cha, chứa các stage con. Các stage con có thể hiểu như các bước thực thi.
- steps : Là block con bên trong block stage là các action thực thi các công việc cần thiết cho mỗi stage
- Trông mỗi step, gọi lệnh build fastlane: 
```
sh "fastlane ios build_staging"
sh "fastlane android build_staging" 
```
Sau khi chạy lệnh này fastlane sẽ build và distribute app lên AppCenter