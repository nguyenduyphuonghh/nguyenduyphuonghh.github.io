---
layout: article
title: Tìm hiểu DevOps và áp dụng thực tế Circle CI
tags: [devops, circle ci, continuous integration, continuous delivery]
---
Trong quá trình phát triển phần mềm, CI/CD có vai trò rất quan trọng trong việc đảm bảo phát triển phần mềm liên tục, mà vẫn giữ được chất lượng sản phẩm tốt. Trong bài viết này tôi sẽ trình bày những hiểu biết về DevOps và áp dụng Circle CI để:
- Test tự động (Java)
- Push kết quả lên kênh chat trên Slack

## 1. Tìm hiểu về DevOps
### 1.1. Giới thiệu về DevOps
**DevOps là gì?**
DevOps là viết tắt của Development (Dev) và Operations (Ops). DevOps là một văn hóa làm việc kết hợp giữa kỹ sư phát triển phần mềm (dev) với bộ phận operator (kỹ sư hệ thống, nhân viên bảo mật, kỹ sư mạng, kỹ sư hạ tầng,...) nhằm mục đích rút ngắn vòng đời phát triển sản phẩm (SDLC).

DevOps không phải là một Tools hỗ trợ làm việc, mà là một phương pháp làm việc cần đến sự hỗ trợ của các Tools. Ngoài ra, không có bất cứ một tiêu chuẩn nào để những những người sử dụng phương pháp DevOps để tuân theo. Nó chỉ đơn giản là một văn hóa, một cách làm việc kết hợp giữa dev và nhân viên operations thay vì làm việc một mình hay chỉ một team độc lập nào đó.

Dưới đây sẽ giới thiệu về những điều cần biết để áp dụng DevOps, bao gồm:
- **DevOps Culture**: văn hóa làm việc kết hợp giữa Dev và Ops
- **DevOps Practices**: cách để thực hiện DevOps
- **DevOps Tools**: những Tools cần để thực hiện DevOps
- **DevOps và Cloud**: mối quan hệ giữa DevOps và cloud

### 1.2. DevOps Culture
Với cách làm việc truyền thống trước đây - dev làm việc riêng và operations làm việc riêng, họ sẽ có mục tiêu công việc khác nhau. Nếu như mục tiêu của dev là xây dựng function đúng với requirement và release cho khách hàng càng nhanh càng tốt thì mục tiêu của operations là tạo ra một hệ thống ổn định. Trong một hoàn cảnh nào đó, hai mục tiêu này sẽ đối lập nhau, function có thể chạy tốt lúc release nhưng sau một thời gian có thể xuất hiện nhiều lỗi hoặc chạy chậm. Hoặc một sản phẩm suy nghĩ nhiều về maintaince system sẽ làm chậm thời gian release. Vì thế, DevOps được sinh ra với ý tưởng là kết hợp Dev và Ops mới một mục tiêu chung là “Speed and Stability” - Tốc độ và ổn định.

Vậy khi có 1 team DevOps, những mục tiêu đạt đến cụ thể như sau:
- **Fast time - to market (TTM)**: code nhanh, deploy nhanh.
- **Few production failures**: khi sản phẩm được đảm bảo về tính ổn định ngay từ đầu thì lỗi sẽ ít xảy ra hơn.
- **Immediate recovery from failures**: không may nếu xảy ra lỗi, vì chúng ta có 1 team đầy đủ dev và ops nên có thể nhanh chóng nhận định nguyên nhân lỗi là do program hay do cấu trúc hệ thống,... và có thể nhanh chóng giải quyết vấn đề ngay lập tức.

Trong DevOps culture, cả dev và operations đều phải quan tâm đến tính ổn định và tốc độ của sản phẩm.Vì thế, dev và operations sẽ phải làm việc cùng nhau, sử dụng những tools kiểm tra tốc độ và tính ổn định của sản phẩm, nhờ vậy mà có thể tạo ra những sản phẩm tốt hơn.
### 1.3. DevOps Concepts và Practices
#### 1.3.1. Build Automation
Build Automation là một quy trình tự động để chuẩn bị source code deploy lên môi trường bằng cách sử dụng script hoặc tool. Tùy vào ngôn ngữ được sử dụng mà cần phải compile, transform hoặc thực hiện unit test,... đối với code. Thông thường build automation cũng giống như việc chạy một command - line tool để chạy doạn code đã được viết script hoặc được setting trong file config. Việc build automation không nên bị phụ thuộc vào IDE cũng như những config của máy tính. Nó có nghĩa là code của bạn có thể được build trên bất cứ PC nào, dù là của bạn hay của người khác.

Vậy tại sao phải build automation? Trước tiên, build automation sẽ giúp tiết kiệm thời gian, có thể handle được những task cần phải build theo một quy trình nhất định nào đó. Việc build automation cũng đảm bảo code sẽ luôn được build theo một quy trình chuẩn, mà ko xảy ra lỗi do nhầm lẫn hay một nguyên nhân nào đó. Ngoài ra, build automation còn giúp thực hiện việc build code trên bất kỳ PC nào, bất cứ ai trong team cũng có thể làm được. Nó giống như việc bạn cho 1 file lên server shared và bất cứ ai có quyền cũng có thể truy cập, sử dụng file đó.
#### 1.3.2. Continuous Intergration (CI - Tích hợp liên tục)
CI là phương pháp đòi hỏi các developer phải thường xuyên merge code thay đổi. Với cách làm việc truyền thống, các developer sẽ làm việc riêng biệt với nhau và sau một khoảng thời gian nhất định chẳng hạn như 1 tuần họ sẽ tiến hành merge code. Tuy nhiên, với CI thì developer phải merge code của họ mỗi ngày và sẽ chạy auto test để detect những vấn đề khi merge code. CI cũng được tự động hóa, và thông thường được hỗ trợ bở một CI Server. Khi developer commit source code thay đổi của họ lên, CI Server sẽ thấy sự thay đổi này và bắt đầu thực hiện build, test source code thay đổi một cách tự động. Quá trình này sẽ được thực hiện nhiều lần trong 1 ngày, và nếu CI server phát hiện có vấn đề xảy ra nó sẽ ngay lập tức hiển thị thông báo cho developer.

Trường hợp có một người khác đưa code của họ lên và xảy ra lỗi trong quá trình build hệ thống sẽ thông báo lỗi cho người đó tiến hành fix, đồng thời sẽ rollback lại để không làm ảnh hưởng đến những người khác.

ậy lợi ích của CI là gì? Đầu tiên, CI sẽ giúp phát hiện ra bug sớm, thông báo cho developer. Developer có thể fix ngay lập tức hoặc rollback để không làm ảnh hưởng đến người khác. Tiếp theo, áp dụng CI sẽ giúp tránh việc phải merge một lượng code lớn khi release. Thay vào đó code sẽ được merge tự động mỗi ngày. Đồng thời, nhờ vào việcì code được merge mỗi ngày nên chúng ta có thể release thường xuyên chứ không cần chờ đến cuối giai đoạn khi tất cả mọi thứ đã xong xuôi mới có thể release được. Khi code được build liên tục nó cũng tạo ra Continuos Testing (Test liên tục), QA có thể test ngay lập tức những chỉnh sửa đã được đưa lên mà không cần chờ đến khi mọi thứ hoàn thành. CI tạo ra một thói quen tốt cho develop, việc thường xuyên commit sẽ làm developer viết ra những đoạn code đơn giản, đúng chuẩn ko rườm ra.

#### 1.3.3. Continuous Delivery và Continuous Deployment
Continuous Delivery (CD) là method đảm bảo code có thể được deploy bất cứ lúc nào. Thay vì phải quyết định có nên deploy code hay không thì team phải build, merge, test,… để đảm bảo code luôn ở trạng thái có thể deploy.

Một số người khi nhắc đến Continuous Delivery thường viết tắt là CD, tuy nhiên cách gọi tắt này sẽ khiến nhầm lẫn giữa Continuous Delivery và Continuous Deployment.

Continuous Deployment là một practice đảm bảo code thay đổi (có size nhỏ) được deploy liên tục lên product.

Vậy Continuous Delivery và Continuous Deployment khác nhau như thế nào ? Continous Delivery đảm bảo cho code có thể deploy bất cứ lúc nào. Trong khi Continuous Deployment là deploy thực tế lên product nhiều lần trong ngày.

Trên thực tế Continuous Delivery và Continuous Deployment được thực hiện theo flow sau:
- Mỗi version code đưa lên đều trải qua một quy trình giống nhau, bao gồm: build automation, test automation,... Và nếu không có vấn đề gì xảy ra nghĩa là code đó có thể deploy.
- Sau khi quyết định deploy thì sẽ thực hiện deploy tự động (deploy automation).
- Nếu việc deploy gặp lỗi, hệ thống sẽ tự động rollback.
- Developer sẽ nhanh chóng fix lỗi và redeploy version fix lỗi.

Với cách làm như vậy Continuous Delivery và Continuous Deployment sẽ đem lại những lợi ích như dưới đây:
- Nhanh chóng đưa sản phẩm đến tay khách hàng
- Giảm thiếu các vấn đề xảy ra khi deploy
- Giảm thiếu risk: lượng deploy trong 1 lần càng nhiều, risk càng cao. Việc chia nhỏ lượng deploy sẽ giảm thiểu risk.
- Rollback lập tức khi xảy ra lỗi
- Giúp developer không còn lo lắng khi deploy khi đã có chức năng roll back automation.

#### 1.3.4. Infrastructure as Code (IaC)
Infrastructure as Code là method giúp quản lý, xây dựng cơ sở hạ tầng (Infra) cho sản phẩm của bạn. Với Infrastructure as Code, thay vì thực hiện thủ công theo manual. Bạn có thể viết code và cho chạy tự động.
Các đối tượng xây dựng trong Infrastructure bao gồm: server, istance, môi trường, container, ...

**Vì sao nên sử dụng IaC?**
1. Đầu tiên, IaC đảm bảo việc tạo và quản lý resource đúng tiêu chuẩn. Tất cả mọi việc được thực hiện automation, ko có những sai sót nếu thực hiện bằng tay.
2. Tính linh hoạt: IaC cho phép thực hiện cùng một thay đổi giống nhau trên nhiều host, và có thể sử dụng lại trong tương lai.
3. Tính co dãn (Scalability) : khi cần thêm instance bạn chỉ cần cho chạy lại config giống với instance có sẵn và một instance mới được tạo ra chỉ trong vài phút hoặc vài giây.
4. Self-documenting: với IaC để xem những config của hệ thống chỉ cần xem trong source control, bạn không cần phải log lại hoặc tạo 1 tài liệu bất kỳ nào để lưu giữ thông tin config. Chính bản thân code được dùng để build infra chính là document.

## 2. Giới thiệu về Circle CI
### 2.1. Đôi nét về CircleCI
CircleCI là 1 công cụ CI được tin dùng bởi hơn 100,000 công ty và developers trên toàn thế giới, trong đó có Facebook, Segment, Kickstarter, Percolate, Spotify, ...
Với những ưu điểm như sau:
- **Faster Performance**: Có thể tùy chọn resource để tối ưu performance cho quá trình building, testing và deploying code
- **Granular Control**: Có thể build theo các cách tùy thích bằng cách sử dụng custom job với Workflows
- **Complete Flexibility**: Tất cả ngôn ngữ chạy trên Linux thì đều chạy được với CricleCI. Hỗ trợ First-class Docker giúp ta có thể tùy chỉnh môi trường tùy thích

CricleCI tương thích với nhiều sản phẩm và dịch vụ được sử dụng nhiều trong quá trình phát triển phần mềm
- **Source Code**: GitHub, Atlassian Bitbucket, GitHub Enterprise
- **Tương thích**: Tương thích với rất nhiều test tool
- **Deployment**: Heroku, Docker, AWS, Azure, Google Cloud, ...
- **Collaborations**: Jira, HipChat, Slack, ...

Ngoài bản Enterprise, CricleCI có Free bao gồm:
- Build on Linux
- Không giới hạn repos và user
- 1,500 build minutes per month
- 1 container
- 1 concurrent build

Bản free khá thoải mái cho những dự án nhỏ.

Circle CI là 1 tool để giúp ta hiện thực hóa CI. Có nhiều tool CI khác cũng nổi tiếng nữa (Travis CI, Jenkins...).

Circle CI sử dụng docker, trong cấu hình Circle CI ta sẽ chỉ định các docker image sẽ sử dụng và các job, trong các job lại có các step, trong các step là cụ thể các command. Ngoài ra còn có cấu hình filter giúp ta linh hoạt điều chỉnh sao cho chỉ run các job khi có merge/push vào 1 số branch nhất định vân vân.

Mô tả quá trình run job trên Circle CI:
1. Developer chỉ cần ```push``` hoặc ```merge``` vào 1 branch, Circle CI tự động biết event đó và khởi động lên job đã được cài đặt tương ứng.
2. Ban đầu Circle CI ```pull docker image``` về và run lên trên môi trường cloud của nó.
3. iếp theo nó chạy các step đã được cài đặt trong docker container, thông thường step đầu tiên luôn là checkout tức là git checkout lấy source về (mặc định lưu trong thư mục ```~/project```)
4. Các step tiếp theo được chạy tùy vào độ sáng tạo của bạn, ví dụ job để build thì thường là npm install rồi ```npm run``` hay job để deploy thì có thể là aws s3 sync hay serverless deploy...
5. Sau khi tất cả các step đã chạy xong, job kết thúc. Nếu exit code của job là error thì mặc định ta sẽ nhận được mail thông báo failed nữa.

Sau khi cài đặt và cấu hình ta chỉ việc dev còn các công việc như build, chạy test, deploy vân vân được tự động hóa hoàn toàn và chạy tức thì trên môi trường cloud mạnh mẽ miễn phí của Circle CI.

## 3. Áp dụng
Sau đây mình sẽ sử dụng Circle CI để cài đặt CI giúp mình tự động deploy và publish trang blog mỗi khi có push thay đổi gì đó lên github.

{:.info}
Blog của mình viết bằng ruby sử dụng template Jekyll thông qua github page.

Quá trình thực hiện:
1. Thiết lập Circle CI: Tạo một folder ```.circleci``` và tạo 1 file ```config.yml``` trong thư mục vừa tạo.
2. Tiến hành push lên github: Push file cấu hình circleci lên reposiroty trên github.
3. Theo dõi quá trình build và deploy.

### 3.1. Cấu hình Circle CI
**Bước 1: Đăng ký/đăng nhập Circle liên kết reposiroty Github**

Tiến hành truy cập trang chủ circle ci tại [đây](https://circleci.com/) để đăng nhập. Bạn có thể đăng nhập bằng tài khoản github hoặc bitbucket.

Chọn **Add projects** và chọn reposiroty.

**Bước 2: Thiết lập Environment Variables**

Chọn project và chọn setting. Trong mục setting chọn mục build setting chọn tiếp vào Environment Variables.

![Environment Variables](/assets/images/environment-variables.PNG)

Tiến hành tạo 2 giá trị trong Environment Variables. ```USERNAME``` là username github hoặc email cũng được, ```PASSWORD``` là password tài khoản github.

![Environment Variables Github](/assets/images/environment-variables-github.PNG)

**Bước 3: Cấu hình .circleci/config.yml**

Trước hết tạo file config.yml Circle CI. Nội dung của file config.yml.
```
version: 2
jobs:
  deploy:
    docker:
      - image: circleci/ruby:2.4.3-node-browsers
    steps:
      - checkout
      - run: npm install
      - run: bundle install
      - run: bundle exec jekyll build
      - deploy:
          name: Deploy Release to GitHub
          command: |
            if [ $CIRCLE_BRANCH == 'develop' ]; then
              bash .circleci/setup-github.sh
            fi
workflows:
  version: 2
  build:
    jobs:
      - deploy
```
Trong file này sẽ sử dụng images ```circleci/ruby:2.4.3-node-browsers``` sau đó:
1. ```bundle install``` sẽ thực hiện cài đặt cái gói bundle cần thiết.
2. ```bundle exec jekyll build``` tiến hành build Jekyll
3. ```Deploy Release lên GitHub``` tiến hành deploy lên Github. Để deploy lên github sẽ chạy file ```setup-github.sh``` trong thư mục ```.circleci```.

**Bước 3: Cấu hình setup-github.sh**
```
git config user.name "$USERNAME"
git config user.email "$PASSWORD"

git checkout master
git pull origin master

find . -maxdepth 1 ! -name '_site' ! -name '.git' ! -name '.gitignore' -exec rm -rf {} \;
mv _site/* .
rm -R _site/

git add -fA
git commit --allow-empty -m "$(git log develop -1 --pretty=%B)"
git push origin master

echo "deployed successfully"
```
**Bước 4: push code lên github**
Để push lên github chúng ta sẽ sử dụng lệnh push lên branch ```master```. Khi có 1 commit mới, Circle CI sẽ tự động build và deploy lên github page (đoạn deploy bản chất là push source lên github page). Để ý trong ```Job``` tại đây sẽ hiển thị kết quả build.

### 3.2. Hook notification về Slack
Với mỗi lần push code lên GitHub, CricleCI sẽ tự động build và chạy test. Sau khi xong, mặc định sẽ tự động gửi kết quả tới mail của mình.

Tuy nhiên để có thể giao tiếp dễ dàng với các member khác trong team, ta có thể setting để kết quả của CricleCI sẽ tự động gửi đến kênh chat của Slack.

Ở App Directory của Slack ta có thể tìm kiếm và cài đặt CircleCI. Sau đó chọn kênh chat mà CricleCI sẽ gửi kết quả. Add CricleCI Intergration và ta sẽ có 1 webhook URL.

![Configurations Slack Circle CI](/assets/images/config-slack-circle-ci.PNG)

Tiếp đến, trên CricleCI ta vào Project và chọn setting cho project cần notification tới Slack. Chọn Chat notifications và ta có thể thấy CricleCI tương thích với khá nhiều tool như Slack, Hipchat, Flowdock, Campfire, IRC, ...

Và kết quả thông bác về channel Slack !

![Message Slack](/assets/images/message-slack.PNG)

Như vậy, cứ mỗi lần ta push code, CricleCI sẽ tự build và test rồi thông báo kết quả lên Slack. Nó giúp cho việc phát triển của team thuận tiện hơn rất nhiều.