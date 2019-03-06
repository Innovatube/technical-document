Mình thấy bài viết này[basic jenkins pipeline.](https://blog.vietnamlab.vn/2017/11/22/huong-dan-tao-jenkinsfile/) và [beginer jenkins](https://viblo.asia/p/jenkins-pipeline-for-beginners-Qbq5QgJJZD8)   giới thiệu rất chi tiết về jenkins về  nên mình sẽ không giới thiệu và hướng dẫn về jenkins pipeline nữa. Bài viết này mình sẽ giới thiệu và cách setup jenkins với reactjs.

[Offical Document](https://jenkins.io/doc/book/pipeline/syntax/)

Để setup Jenkins với React thì việc sử dụng docker và bash shell rất quan trọng. Nên cần tìm hiểu 2 cái đó trước khi đến với bài viết này.


* 1 file Jenkinsfile có pipeline sẽ dạng như sau:

```
@Library([
    'slack-message',
    'elastic-beanstalk'
]) _

pipeline {
    agent any
    options {
        disableConcurrentBuilds()
    }
    triggers {
        pollSCM('* * * * *')
        upstream(upstreamProjects: 'Wassha-external/develop', threshold: hudson.model.Result.SUCCESS)
    }
    environment {
        PROJECT_NAME = 'Wassha-Internal'
        AWS_KEY = credentials('wassha_aws_key')
        TER_SLACK_HOOK_KEY = 'xxxxxxxxxx'
        TER_SLACK_CHANNEL = '#jenkins-wassha'
        GITLAB_CREDENTIAL_ID = '***************'
    }
    stages {
        stage('Inform start') {
            steps {
                script {
                    terSlack.infoMessage(message: "[$PROJECT_NAME] Build Started", color: "warning")
                }
            }
        }
        stage('Clone frontend') {
            steps {
                dir('Frontend') {
                    git url: 'ssh://git@gitlab.com/external_website.git', branch: 'develop', credentialsId: GITLAB_CREDENTIAL_ID
                    sh "ls -la"
                }
            }
        }
        stage('Build docker') {
            when {
                anyOf { branch 'develop'; branch 'staging' } 
            }
            steps {
                script {
                        sh "docker-compose up --build -d"
                }
            }
        }

        stage('Deploy develop') {
            when {
                branch('develop')
            }
            steps {
                script {
                    sh "docker-compose exec -T deploy npm install"
                    sh "docker-compose exec -T deploy sh exec-frontend.sh"
                    sh "docker-compose exec -T deploy ls ./public/external/img/homepage"
                    sh "docker-compose exec -T deploy npm run production"
                    sh "docker-compose exec -T deploy sh /root/bin/profile.sh ${AWS_KEY_USR} ${AWS_KEY_PSW}"
                    sh "docker-compose exec -T deploy /root/.local/bin/eb init Wassha --region us-east-1 --platform php-7.1"
                    sh "docker-compose exec -T deploy /root/.local/bin/eb deploy wassha-dev"
                    terSlack.infoMessage(message: "[$PROJECT_NAME] Build Finished", color: "good")
                }
            }
        }

        stage('Deploy Staging') {
            when {
                branch('staging')
            }
            steps {
                script {
                    sh "docker-compose exec -T deploy npm install"
                    sh "docker-compose exec -T deploy sh exec-frontend.sh"
                    sh "docker-compose exec -T deploy ls ./public/external/img/homepage"
                    sh "docker-compose exec -T deploy npm run production"
                    sh "docker-compose exec -T deploy sh /root/bin/profile.sh ${AWS_KEY_USR} ${AWS_KEY_PSW}"
                    sh "docker-compose exec -T deploy /root/.local/bin/eb init Wassha --region us-east-1 --platform php-7.1"
                    sh "docker-compose exec -T deploy /root/.local/bin/eb deploy Wassha-staging-1"
                    terSlack.infoMessage(message: "[$PROJECT_NAME] Build Finished", color: "good")
                }
            }
        }
    }
    post {
        always {
            sh "docker-compose down --rmi all"
        }
    }
}
```

Mình sẽ giải thích ý nghĩa vài lệnh bên trên example trên:

* `@Library` khai báo việc import library vào trong jenkins.

* [agent](https://jenkins.io/doc/book/pipeline/syntax/#agent) : chỉ định môi trường sẽ thực thi các thao tác trong steps

* [option](https://jenkins.io/doc/book/pipeline/syntax/#options): các tùy chọn cho việc check và build. Như trong trường hợp này mình dùng `disableConcurrentBuilds` nó sẽ ko cho phép thực hiện đồng thời các tác vụ cùng lúc. 

* [triggers](https://jenkins.io/doc/book/pipeline/syntax/#triggers): Chỉ được đặt trong block pipeline, định nghĩa Pipeline được trigger để chạy như thế nào, hỗ trợ 3 kiểu là: cron, pollSCM and upstream.

    * cron: chạy Pipeline định kỳ, cú pháp là cú pháp của cron linux ( 0 * * * * : là chạy mỗi giờ đúng chẳng hạn)

    * pollSCM: định lỳ check source trên repo xem có j thay đổi ko, ví dụ triggers { pollSCM('H */4 * * 1-5') } là kiểm tra vào phút 0,15,30,45 mỗi giờ từ thứ 2->6

    * upstream: được trigger chạy Pipeline khi 1 project khác được build xong. Ví dụ triggers { upstream(upstreamProjects: 'job1,job2', threshold: hudson.model.Result.SUCCESS) } là khi 1 trong 2 job1,job2 chạy xong mà thành công thì sẽ chạy Pipeline. Trong truờng hợp này mình muốn repo `Wassha-external/develop` được build trước.

* [environment](https://jenkins.io/doc/book/pipeline/syntax/#environment): được khai báo trong block pipeline hoặc stage, là 1 chuỗi các cặp Key-Val định nghĩa các biến môi trường cho toàn bộ (global - nếu để tại scope pipeline) hoặc cho 1 stage riêng ( nếu được khai báo trong block stage đấy ). Có hàm credentials() để lấy thông tin xác thực nhạy cảm (biến định nghĩa ra sẽ ko thể xem được raw text của nó mà chỉ thấy được **** nhưng có thể sử dụng để xác thực được. Ở đây mình khai báo các biến cho cả slack library, aws key và gitlab.

* [stages](https://jenkins.io/doc/book/pipeline/syntax/#stages): Mình khai báo 1 stage cha, chứa các stage con. Các stage con có thể hiểu như các bước thực thi.

* [steps](https://jenkins.io/doc/book/pipeline/syntax/#steps) : Là block con bên trong block stage là các action thực thi các công việc cần thiết cho mỗi stage

* [script](https://jenkins.io/doc/book/pipeline/syntax/#script): Jenkins được viết bằng `groovy script`, nên nếu muốn chèn một đoạn groovy script, bạn phải dùng thẻ script {} như ví dụ steps ở bên.

* `dir('Frontend')`: Dòng này để tạo 1 folder tên là `Frontend` và các dòng trong nó là clone git repo về.

* [when](https://jenkins.io/doc/book/pipeline/syntax/#when):  chỉ được đặt trong block `stage`, định nghĩa điều kiện (condition) để thực hiện các thực thi stage đó, có thể có 1 hoặc nhiều điều kiện, với nhiều điều kiện thì phải thỏa mãn tất cả . Hỗ trợ các điều liện lồng nhau (nested) bằng các cú pháp `not`- ko thỏa mãn điều kiện , `allOf` - thỏa mãn tất cả điều kiện, và `anyOf` - thỏa mãn 1 trong các điều kiện.

* `sh "docker-compose exec -T deploy npm install"`: Dòng này thực thi docker-compose command. Như mình đã nói bên trên, Jenkins build cho react sẽ được thực thi bởi hầu hết các câu lệnh docker và bash shell.

* [post](https://jenkins.io/doc/book/pipeline/syntax/#post): Cái sẽ được chạy cuối cùng khi kết thúc Pipeline hoặc stage (giống như finally trong Java đó) Nhằm handle kết quả của pipeline hoặc chạy các tác vụ cần chạy sau cùng. Ở ví dụ trên, mình sẽ chạy lệnh docker compose để shutdown và xóa hết các image mà nó đã tạo ra.


* Ở Jenkinsfile này mình chia 2 stage chính đó là `Build docker` và `Deploy`. 
`Build docker` mình chỉ thực hiện để build không thôi, không bao gồm deploy.
`Deploy develop` và `Deploy staging` là 2 stage để deploy sau khi check việc build docker ok.

### Setup React:
Việc setup Jenkins cho react nó cũng giống như việc bạn vừa clone 1 project từ github hay gitlab hay bất kỳ nơi nào về và sau đó bạn làm các bước để chạy được con react đó.

Các bước thường thực hiện cho 1 prj react vừa mới clone về thường sẽ như sau:
+ pull các thư viện về: `npm install`
+ run project: `npm start`
+ build project: `npm run build` ( dùng khi muốn deploy )

> FLow sẽ được setup như sau: Từ Jenkinsfile => docker-compose.yml => Dockerfile

Jenkinsfile:

```sh
@Library([
    'slack-message',
    'elastic-beanstalk'
]) _

pipeline {
    agent any
    environment {
        PROJECT_NAME = 'Wassha-External'
        AWS_KEY = credentials('wassha_aws_key')
        TER_SLACK_HOOK_KEY = 'T0D5XQZMY/BF414DNJC/9Rvqau0XA3XDoWjsL5SKk6Se'
        TER_SLACK_CHANNEL = '#jenkins-wassha'
    }
    stages {
        stage('Inform slack') {
            steps {
                script {
                    terSlack.infoMessage(message: "[$PROJECT_NAME] Verify Starting....", color: "warning")
                }
            }
        }
        stage('Build docker') {
            when {
                branch('develop')
            }
            steps {
                script {
                    sh "docker-compose -f .cicd/docker/docker-compose.yml up --build -d"
                }
            }
        }
    }
    post {
        always {
            sh "docker-compose -f .cicd/docker/docker-compose.yml down"
        }
    }
}
```

* Mình chia 2 stage: 
    * `stage('Inform slack')`: Thông báo slack cho người dùng biết nó đã bắt đầu builld
    * `stage('Build docker')`: Dùng lệnh docker-compose để thực thi

Trong script mình dùng: `sh "docker-compose -f .cicd/docker/docker-compose.yml up --build -d"`

`-f`: chỉ định đường dẫn file docker-compose.yml
`--build -d`: build và chạy background
* Và cuối cùng mình down composer trong `post`

Ở trong `docker-compose.yml` file sẽ như sau:

```sh
version: '3.4'
services:
  web:
    build:
      context: ../..
      dockerfile: ./.cicd/docker/web/Dockerfile
```

* Ở đây mình khai báo 1 service là web
* trong `build` mình chỉ định path dockerfile và context

Ở `Dockerfile`:

```sh
FROM node:8
WORKDIR /usr/src/app
COPY . .
RUN npm install
RUN npm run build
CMD ["npm", "start"]
```

Về structure cho cicd hiện tại mình đang để như sau:

```
--.cicd
        -docker
                -web
                    -Dockerfile
                -docker-compose.yml
        -jenkins
                -Jenkinsfile
```

Và bài viết trên được để theo đường dẫn như trên, các bạn có thể tham khảo.

* Chú ý: CMD chỉ thực hiện sau khi build image.