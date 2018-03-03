
[中文版](./README-zh.md)
 | [日本語版](./README-ja.md)
 | [한국어](./README-ko.md)

[<img src="./images/elsewhen-logo.png" width="180" height="180">](http://elsewhen.co/)


# Project Guidelines &middot; [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
> While developing a new project is like rolling on a green field for you, maintaining it is a potential dark twisted nightmare for someone else.
Here's a list of guidelines we've found, written and gathered that (we think) works really well with most JavaScript projects here at [elsewhen](http://elsewhen.co).
If you want to share a best practice, or think one of these guidelines should be removed, [feel free to share it with us](http://makeapullrequest.com).
- [Git](#git)
    - [Some Git rules](#some-git-rules)
    - [Git workflow](#git-workflow)
    - [Writing good commit messages](#writing-good-commit-messages)
- [Documentation](#documentation)
- [Environments](#environments)
    - [Consistent dev environments](#consistent-dev-environments)
    - [Consistent dependencies](#consistent-dependencies)
- [Dependencies](#dependencies)
- [Testing](#testing)
- [Structure and Naming](#structure-and-naming)
- [Code style](#code-style)
    - [Some code style guidelines](#code-style-check)
    - [Enforcing code style standards](#enforcing-code-style-standards)
- [Logging](#logging)
- [API](#api)
    - [API design](#api-design)
    - [API security](#api-security)
    - [API documentation](#api-documentation)
- [Licensing](#licensing)

<a name="git"></a>
## 1. Git
![Git](/images/branching.png)
<a name="some-git-rules"></a>

### 1.1 Some Git rules
There are a set of rules to keep in mind:
* Perform work in a feature branch.
    
    _Tại sao vậy:_
    >Because this way all work is done in isolation on a dedicated branch rather than the main branch. It allows you to submit multiple pull requests without confusion. You can iterate without polluting the master branch with potentially unstable, unfinished code. [read more...](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)
* Branch out from `develop`
    
    _Tại sao vậy:_
    >This way, you can make sure that code in master will almost always build without problems, and can be mostly used directly for releases (this might be overkill for some projects).

* Never push into `develop` or `master` branch. Make a Pull Request.
    
    _Tại sao vậy:_
    > It notifies team members that they have completed a feature. It also enables easy peer-review of the code and dedicates forum for discussing the proposed feature.

* Update your local `develop` branch and do an interactive rebase before pushing your feature and making a Pull Request.

    _Tại sao vậy:_
    > Rebasing will merge in the requested branch (`master` or `develop`) and apply the commits that you have made locally to the top of the history without creating a merge commit (assuming there were no conflicts). Resulting in a nice and clean history. [read more ...](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)

* Resolve potential conflicts while rebasing and before making a Pull Request.
* Delete local and remote feature branches after merging.
    
    _Tại sao vậy:_
    > It will clutter up your list of branches with dead branches. It ensures you only ever merge the branch back into (`master` or `develop`) once. Feature branches should only exist while the work is still in progress.

* Before making a Pull Request, make sure your feature branch builds successfully and passes all tests (including code style checks).
    
    _Tại sao vậy:_
    > You are about to add your code to a stable branch. If your feature-branch tests fail, there is a high chance that your destination branch build will fail too. Additionally, you need to apply code style check before making a Pull Request. It aids readability and reduces the chance of formatting fixes being mingled in with actual changes.

* Use [this](./.gitignore) `.gitignore` file.
    
    _Tại sao vậy:_
    > It already has a list of system files that should not be sent with your code into a remote repository. In addition, it excludes setting folders and files for most used editors, as well as most common dependency folders.

* Protect your `develop` and `master` branch.
  
    _Tại sao vậy:_
    > It protects your production-ready branches from receiving unexpected and irreversible changes. read more... [Github](https://help.github.com/articles/about-protected-branches/), [Bitbucket](https://confluence.atlassian.com/bitbucketserver/using-branch-permissions-776639807.html) and [GitLab](https://docs.gitlab.com/ee/user/project/protected_branches.html)

<a name="git-workflow"></a>
### 1.2 Git workflow
Because of most of the reasons above, we use [Feature-branch-workflow](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow) with [Interactive Rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing) and some elements of [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow) (naming and having a develop branch). The main steps are as follows:

* For a new project, initialize a git repository in the project directory. __For subsequent features/changes this step should be ignored__.
   ```sh
   cd <project directory>
   git init
   ```

* Checkout a new feature/bug-fix branch.
    ```sh
    git checkout -b <branchname>
    ```
* Make Changes.
    ```sh
    git add
    git commit -a
    ```
    _Tại sao vậy:_
    > `git commit -a` will start an editor which lets you separate the subject from the body. Read more about it in *section 1.3*.

* Sync with remote to get changes you’ve missed.
    ```sh
    git checkout develop
    git pull
    ```
    
    _Tại sao vậy:_
    > This will give you a chance to deal with conflicts on your machine while rebasing (later) rather than creating a Pull Request that contains conflicts.
    
* Update your feature branch with latest changes from develop by interactive rebase.
    ```sh
    git checkout <branchname>
    git rebase -i --autosquash develop
    ```
    
    _Tại sao vậy:_
    > You can use --autosquash to squash all your commits to a single commit. Nobody wants many commits for a single feature in develop branch. [read more...](https://robots.thoughtbot.com/autosquashing-git-commits)
    
* If you don’t have conflicts, skip this step. If you have conflicts, [resolve them](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/)  and continue rebase.
    ```sh
    git add <file1> <file2> ...
    git rebase --continue
    ```
* Push your branch. Rebase will change history, so you'll have to use `-f` to force changes into the remote branch. If someone else is working on your branch, use the less destructive `--force-with-lease`.
    ```sh
    git push -f
    ```
    
    _Tại sao vậy:_
    > When you do a rebase, you are changing the history on your feature branch. As a result, Git will reject normal `git push`. Instead, you'll need to use the -f or --force flag. [read more...](https://developer.atlassian.com/blog/2015/04/force-with-lease/)
    
    
* Make a Pull Request.
* Pull request will be accepted, merged and close by a reviewer.
* Remove your local feature branch if you're done.

  ```sh
  git branch -d <branchname>
  ```
  to remove all branches which are no longer on remote
  ```sh
  git fetch -p && for branch in `git branch -vv --no-color | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
  ```

<a name="writing-good-commit-messages"></a>
### 1.3 Writing good commit messages

Having a good guideline for creating commits and sticking to it makes working with Git and collaborating with others a lot easier. Here are some rules of thumb ([source](https://chris.beams.io/posts/git-commit/#seven-rules)):

 * Separate the subject from the body with a newline between the two.

    _Tại sao vậy:_
    > Git is smart enough to distinguish the first line of your commit message as your summary. In fact, if you try git shortlog, instead of git log, you will see a long list of commit messages, consisting of the id of the commit, and the summary only.

 * Limit the subject line to 50 characters and Wrap the body at 72 characters.

    _why_
    > Commits should be as fine-grained and focused as possible, it is not the place to be verbose. [read more...](https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c)

 * Capitalize the subject line.
 * Do not end the subject line with a period.
 * Use [imperative mood](https://en.wikipedia.org/wiki/Imperative_mood) in the subject line.

    _Tại sao vậy:_
    > Rather than writing messages that say what a committer has done. It's better to consider these messages as the instructions for what is going to be done after the commit is applied on the repository. [read more...](https://news.ycombinator.com/item?id=2079612)


 * Use the body to explain **what** and **why** as opposed to **how**.

 <a name="documentation"></a>
## 2. Documentation

![Documentation](/images/documentation.png)

* Use this [template](./README.sample.md) for `README.md`, Feel free to add uncovered sections.
* For projects with more than one repository, provide links to them in their respective `README.md` files.
* Keep `README.md` updated as a project evolves.
* Comment your code. Try to make it as clear as possible what you are intending with each major section.
* If there is an open discussion on github or stackoverflow about the code or approach you're using, include the link in your comment. 
* Don't use comments as an excuse for a bad code. Keep your code clean.
* Don't use clean code as an excuse to not comment at all.
* Keep comments relevant as your code evolves.

<a name="environments"></a>
## 3. Environments

![Environments](/images/laptop.png)

* Define separate `development`, `test` and `production` environments if needed.

    _Tại sao vậy:_
    > Different data, tokens, APIs, ports etc... might be needed in different environments. You may want an isolated `development` mode that calls fake API which returns predictable data, making both automated and manual testing much easier. Or you may want to enable Google Analytics only on `production` and so on. [read more...](https://stackoverflow.com/questions/8332333/node-js-setting-up-environment-specific-configs-to-be-used-with-everyauth)


* Load your deployment specific configurations from environment variables and never add them to the codebase as constants, [look at this sample](./config.sample.js).

    _Tại sao vậy:_
    > You have tokens, passwords and other valuable information in there. Your config should be correctly separated from the app internals as if the codebase could be made public at any moment.

    _How:_
    >
    `.env` files to store your variables and add them to `.gitignore` to be excluded. Instead, commit a `.env.example`  which serves as a guide for developers. For production, you should still set your environment variables in the standard way.
    [read more](https://medium.com/@rafaelvidaurre/managing-environment-variables-in-node-js-2cb45a55195f)

* It’s recommended to validate environment variables before your app starts.  [Look at this sample](./configWithTest.sample.js) using `joi` to validate provided values.
    
    _Tại sao vậy:_
    > It may save others from hours of troubleshooting.

<a name="consistent-dev-environments"></a>
### 3.1 Consistent dev environments:
* Set your node version in `engines` in `package.json`.
    
    _Tại sao vậy:_
    > It lets others know the version of node the project works on. [read more...](https://docs.npmjs.com/files/package.json#engines)

* Additionally, use `nvm` and create a  `.nvmrc`  in your project root. Don't forget to mention it in the documentation.

    _Tại sao vậy:_
    > Any one who uses `nvm` can simply use `nvm use` to switch to the suitable node version. [read more...](https://github.com/creationix/nvm)

* It's a good idea to setup a `preinstall` script that checks node and npm versions.

    _Tại sao vậy:_
    > Some dependencies may fail when installed by newer versions of npm.
    
* Use Docker image if you can.

    _Tại sao vậy:_
    > It can give you a consistent environment across the entire workflow. Without much need to fiddle with dependencies or configs. [read more...](https://hackernoon.com/how-to-dockerize-a-node-js-application-4fbab45a0c19)

* Use local modules instead of using globally installed modules.

    _Tại sao vậy:_
    > Lets you share your tooling with your colleague instead of expecting them to have it globally on their systems.


<a name="consistent-dependencies"></a>
### 3.2 Consistent dependencies:

* Make sure your team members get the exact same dependencies as you.

    _Tại sao vậy:_
    > Because you want the code to behave as expected and identical in any development machine [read more...](https://medium.com/@kentcdodds/why-semver-ranges-are-literally-the-worst-817cdcb09277)

    _how:_
    > Use `package-lock.json` on `npm@5` or higher

    _I don't have npm@5:_
    > Alternatively you can use `Yarn` and make sure to mention it in `README.md`. Your lock file and `package.json` should have the same versions after each dependency update. [read more...](https://yarnpkg.com/en/)

    _I don't like the name `Yarn`:_
    > Too bad. For older versions of `npm`, use `—save --save-exact` when installing a new dependency and create `npm-shrinkwrap.json` before publishing. [read more...](https://docs.npmjs.com/files/package-locks)

<a name="dependencies"></a>
## 4. Dependencies

![Github](/images/modules.png)

* Keep track of your currently available packages: e.g., `npm ls --depth=0`. [read more...](https://docs.npmjs.com/cli/ls)
* See if any of your packages have become unused or irrelevant: `depcheck`. [read more...](https://www.npmjs.com/package/depcheck)
    
    _Tại sao vậy:_
    > You may include an unused library in your code and increase the production bundle size. Find unused dependencies and get rid of them.

* Before using a dependency, check its download statistics to see if it is heavily used by the community: `npm-stat`. [read more...](https://npm-stat.com/)
    
    _Tại sao vậy:_
    > More usage mostly means more contributors, which usually means better maintenance, and all of these result in quickly discovered bugs and quickly developed fixes.

* Before using a dependency, check to see if it has a good, mature version release frequency with a large number of maintainers: e.g., `npm view async`. [read more...](https://docs.npmjs.com/cli/view)

    _Tại sao vậy:_
    > Having loads of contributors won't be as effective if maintainers don't merge fixes and patches quickly enough.

* If a less known dependency is needed, discuss it with the team before using it.
* Always make sure your app works with the latest version of its dependencies without breaking: `npm outdated`. [read more...](https://docs.npmjs.com/cli/outdated)

    _Tại sao vậy:_
    > Dependency updates sometimes contain breaking changes. Always check their release notes when updates show up. Update your dependencies one by one, that makes troubleshooting easier if anything goes wrong. Use a cool tool such as [npm-check-updates](https://github.com/tjunnone/npm-check-updates).

* Check to see if the package has known security vulnerabilities with, e.g., [Snyk](https://snyk.io/test?utm_source=risingstack_blog).


<a name="testing"></a>
## 5. Kiểm thử
![Testing](/images/testing.png)
* Cần có 1 môi trường `test` nếu cần thiết.

    _Tại sao vậy_
    > Đôi khi chỉ cần thực thi bước test end to end trên "production" thực tế là xong, nhưng cũng có một vài ngoại lệ như: một ví dụ là có thể bạn không muốn enable các thông tin phân tích được trong chế độ `production` và cũng không muốn tạo dư liệu rác cho trang quản trị bằng các test data. Một ví dụ khác nữa là API của bạn có thể bị hạn chế một phần nào đó trong `production` và khóa các lệnh gọi thử nghiệm của bạn sau 1 số request nhât định nào đó. 
    
* Hãy lưu các file test trong cùng folder với các module đang được test và sử dụng quy tắc đặt tên có dạng`*.test.js` hoặc `.spec.js`, ví dụ: `moduleName.spec.js`.

    _Tại sao vậy:_
    >Bạn không muốn mất công truy cập vào trong một cấu trúc folder để tìm kiếm một unit test. [xem thêm...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)
    
* Hãy đặt các file test bổ sung thêm của mình trong một folder test riêng để tránh sự nhầm lẫn.

     _Tại sao vậy:_
     > Một vài file test không ràng buộc cụ thể với bất kỳ file thực thi nào cả. Bạn sẽ phải lưu nó trong 1 folder để cho các developer khác có thể dễ dàng tìm thấy nhất: folder `__test__`. Tên `__test__` cũng là quy tắc đặt tên chuẩn hiện nay và cũng đưuọc lựa chọn trong hầu hết các framework testing Javascript. 
     
* Hãy viết code có thể test được, tránh ảnh hưởng đến các thành phần liên quan và các thành phần mở rộng khác, viết các function tách bạch.

    _Tại sao vậy:_
    > Bạn muốn kiểm một logic business như các units tách biệt. Bạn phải "tối thiểu hóa sự tác động ngẫu nhiên và các xử lý không xác định tới độ tin cậy của đoạn code". [xem thêm...](https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3)
    
    > Một function tách bạch là 1 function luôn trả về các output giống nhau khi các dũ liệu đầu vào giống nhau. Ngược lại, một function không tách bạch có thể gây ảnh hưởng tới các thành phần liên quan hoặc sẽ phụ thuộc vào các điều kiện từ bên ngoài để xử lý một giá trị nào đó. Điều này làm cho việc dự đoán kết quả khó khăn hơn.  [xem thêm...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)
    
* Hãy dùng một đối tượng kiểm tra kiểu static

   _Tại sao vậy:_
   
   > Đôi khi bạn có thể cần tới một đối tượng kiểm tra kiểu Static. Nó đem lại độ tin cậy nhất định cho đoạn code của bạn [xem thêm...](https://medium.freecodecamp.org/why-use-static-types-in-javascript-part-1-8382da1e0adb)
   
* Hãy chạy các bài test trên máy tính mình trước khi tạo bất kỳ pull request nào tới `develop`

    _Tại sao vậy:_
    >Bạn không muốn là người gây ra lỗi khi build sản phẩm trên nhánh production. Hãy chạy các đoạn test của mình sau khi `rebase` và trước khi đẩy nhánh của bản lên remote repository.
    
* Tài liệu về việc test gồm các hướng dẫn nằm trong từng phần tương ứng trong file`README.md`.

   _Tại sao vậy:_
   > Dây là một tài liệu tiện ích nhỏ mà bạn đưa ra để cho các developer khác, hay các chuyên gia và QA có thể làm việc với code của bạn.
   
<a name="structure-and-naming"></a>
## 6. Cấu trúc và cách đặt tên
![Structure and Naming](/images/folder-tree.png)

* Hãy tổ chức các file của bạn tương ứng với các tính năng / pages / components, của product. Cũng nên lưu các file test cùng folder với file thực thi của chúng.

    **Bad**

    ```
    .
    ├── controllers
    |   ├── product.js
    |   └── user.js
    ├── models
    |   ├── product.js
    |   └── user.js
    ```

    **Good**

    ```
    .
    ├── product
    |   ├── index.js
    |   ├── product.js
    |   └── product.test.js
    ├── user
    |   ├── index.js
    |   ├── user.js
    |   └── user.test.js
    ```

     _Tại sao vậy:_
     > Thay vì liệt kê 1 danh sách dài các file, bạn có thể chia ra thành các module nhỏ và này sẽ bao trọn vai trò chức năng của nó bao gồm chức năng test của nó, và nhiều hơn thế nữa. Điều này dễ dàng cho việc điều hướng và có thể được tìm thấy trong nháy mắt.
       
* Đặt các file test mở rộng cảu bạn trong 1 folder riêng lẻ để tránh nhầm lẫn.

    _Tại sao vậy:_
    > Nó sẽ tiết kiệm thời gian cho các developer khác hay các chuyên gia trong team của bạn.

* Hãy sử dụng 1 folder `./config` và không tạo thêm bất kỳ file config nào khác cho các các môi trường khác nhau.

   _Tại sao vậy:_
   > Khi bạn chia nhỏ 1 file config cho nhưng mục đích khác nhau (database, API,...);
   đặt chúng vào một folder với một tên có ý nghĩa dễ nhận biết như `config`. Chỉ cần nhớ không tạo các file config khác nhau cho các môi trường khác nhau. Nó sẽ không có quy mô rõ ràng, vì khi càng nhiều bản deyloy của app được tạo thì cần càng nhiều tên môi trường mới.
   Các giá trị được sử dụng trong tệp tin config nên được cung cấp bởi các biến môi trường. [xem thêm...](https://medium.com/@fedorHK/no-config-b3f1171eecd5)     

* Đặt các file script trong folder `./scripts`. Folder này sẽ bao gồm các scripts `bash` và `node`.
 
   _Tại sao vậy:_
   >Rất có thể bạn sử dụng với nhiều hơn 1 script, production build, development build, database feeders, database synchronization, ....

* Đặt các output sau khi thực hiện build code trong folder `./build`. Và `build/` vào file `.gitignore`. 
* Place your build output in a `./build` folder. Add `build/` to `.gitignore`.

   _Tại sao vậy:_
   >Đặt tên nó theo bất kỳ thứ gì mà bạn muốn, có thể là `dist`.  Nhưng hãy đảm bảo rằng nó phù hợp với team của bạn. Những gì được tạo ra bằng các cách như (bundled, compliled,transpiled) đều có thể được lưu và lấy từ folder này. Những gì bạn có thể tạo ra, teamate của bạn cũng có thể tạo ra, do đó, không nên commit chúng nên remote repository. Trừ khi bạn thực sự muốn điều đó.

<a name="code-style"></a>
## 7. Code style
    ![Code style](/images/code-style.png)

<a name="code-style-check"></a>
### 7.1 Một vài nguyên tắc về cách viết code

* Hãy sử dụng cú pháp javascript từ stage-2 trở nên cho các project mới. Đối với project trước đó thì vẫn sẽ thích hợp với những cú pháp hiện tại, trừ khi bạn có định làm mới project của mình.

   _Tại sao vậy:_
   > Đây là tất cả mọi thứ cho bạn lựa chọn. Chúng tôi dùng các transpiler để tận dụng những ưu điểm của cú pháp mới. Stage-2 có thể chỉ là một phần của những stage cụ thể nào đó với một vài điểm thay đổi nhỏ.
   
* Thực hiện kiểm tra code style trong quá trình build.

   _Tại sao vậy:_
   > Việc bị phá vỡ trong cấu trúc của bạn là 1 một nguyên nhân để thực hiện code style trong code của bạn. Điều đó sẽ ngăn bạn suy nghĩ xem nhẹ việ áp dụng code style trong code. Hãy làm điều này cho cả code ở phía client và cả server. [xem thêm...](https://www.robinwieruch.de/react-eslint-webpack-babel/)
   
  
* Hãy sử dụng [ESLint - Pluggable JavaScript linter](http://eslint.org/) to áp dụng code style.

    _Tại sao vậy:_
    > Chỉ đơn giản là chúng tôi thích dùng `eslint`, bạn không bắt buộc phải dùng nó. Cũng có nhiều quy tắc khác có thể được hõ trợ, , có thể configure các nguyên tắc, và thêm các rule tùy chỉnh. 

* Chúng tôi dùng [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) đối với JavaScript, [Read more](https://www.gitbook.com/book/duk/airbnb-javascript-guidelines/details). [Xem thêm](https://www.gitbook.com/book/duk/airbnb-javascript-guidelines/details). Hyax áp dụng style javascript trong project hoặc trong team của bạn.

* Chúng tôi dùng [Flow type style check rules for ESLint](https://github.com/gajus/eslint-plugin-flowtype) khi dùng [FlowType](https://flow.org/).

    _Tại sao vậy:_
    > Flow đưa ra một vài cú pháp và nó cần thiết tuân theo trong cả code style và trong check code style.

* Hãy dùng `.eslintignore` để loại trừ các file hay folder không được check code style.

    _Tại sao vậy:_
    > Ban không làm lộn xộn code của mình với những commnent `eslint-disable` bất cứ khi nào bạn cần loại bỏ 1 vài file khỏi công đoạn check style.

* Xóa bỏ hoàn toàn các comment disable `eslint` trước khi tạo ra một Pull request

    _Tại sao vậy:_
    > Thông thường nên disabel các check style khi đang làm việc với 1 khối code để tập trung hơn vào phần xử lý logic. Chỉ cần nhớ là xóa các comment `eslint-disable` và tuân theo những quy tắc này thôi.

* Phụ thuộc vào mức độ của task mà sử dụng comment `//TODO` hoặc tạo 1 ticket

    _Tại sao vậy:_
    > Như thế thì bạn có thể tự nhắc nhở bản thân hay người khác về phần công việc nhỏ (như là sắp xếp lại 1 function hay update 1 comment). Đối với nhưững phàn công việc lớn hơn thì hãy sử dụng `//TODO(#3456)` được tạo ra bởi quy tắc lint và một số.

* Hãy luôn comment và update nó với sự thay của code. Hãy xóa các phần code bị comment đi.     
    _Tại sao vậy:_
    > Code của bạn phải dễ đọc nhất có thể, bạn nên loại bỏ tất cả những thứ làm rối code. Nếu bạn tùy chỉnh lại 1 function thì đừng comment trong function trước đó mà hãy xóa bỏ hoàn toàn nó đi.
    
* Tránh những comment, log, và các tên gọi không liên quan, hài hước.

    _Tại sao vậy:_
    > Trong khi xử lý build của bạn có thể bỏ qua chúng, đôi khi mã nguồn của bạn có thể được đưa cho 1 công ty hay 1 khách hàng khác thì không thể để những lỗi lực cười như thể được.

* Đặt các tên có thể tìm kiếm được với ý nghĩa duy nhất, tránh các tên bị rút gọn. Đối với các function hãy dùng các tên có tính mô tả và dài hơn. Tên function nên là 1 động từ hoặc là 1 cụm đông từ mà ám chỉ chức năng của function đó.

    _Tại sao vậy:_
    > Điều đó giúp đọc mã nguồn dễ hơn.

* Hãy sắp xếp các function của bạn trong 1 file theo nguyên tắc step-down. Những function có level cao hơn nên đặt ở trên, các function có level thấp hơn nên sắp xếp ở dưới.

    _Tại sao vậy:_
    >  Điều đó giúp đọc mã nguồn dễ hơn.

<a name="enforcing-code-style-standards"></a>
### 7.2 Thực hiện các tiêu chuẩn về code style

* Hãy dùng 1 file [.editorconfig](http://editorconfig.org/), file này giúp các developer quy định và duy trì code style thống nhất trên các editor và IDE khác nhau trong project

    _Tại sao vậy:_
    > EditorConfig project bao gồm 1 file để quy định các code style và 1 tập hợp các plugin dùng cho text editor cho phép các editor đọc các file theo định dạng và tuân theo quy tắc đã quy định từ trước. Các file EditorConfig rất dễ đọc và chúng cũng thích hợp với VCS.

* Cần có thông báo tren editor về các lỗi thuộc về code style. Hãy sử dụng [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier) và [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier) cùng với việc config ESLint đang có sẵn. [xem thêm...](https://github.com/prettier/eslint-config-prettier#installation)
     
* Hãy cân nhắc việc sử dụng GIT

    _Tại sao vậy:_
    > Git hook giúp tăng đang kể năng xuất của 1 developer. Taoj ra các thay đổi, comit, và đẩy chúng trong môi trường product hoặc tạm thời mà không cần lo lắng về việc phá vớ cấu trúc. [Xem thêm...](http://githooks.com/)
   
* Hãy sử dụng Prettier với 1 precommit hook.

    _Tại sao vậy:_
    > Trong khi chỉ riêng `prettier` đã rất là mạnh, nó không thực sự hiệu quả để chạy những cái đơn giản nhưn 1 task npm đơn lẻ sau mỗi lần format code. Đây chính là nơi `lint-staged` (và `husky`) đi vào. Đọc thêm về việc cấu hình `lint-staged` [tại đây](https://github.com/okonet/lint-staged#configuration) và config `husky` [tại đây](https://github.com/typicode/husky).
    
<a name="logging"></a>
## 8. Logging

![Logging](/images/logging.png)

* Tránh log consile ở phía client trong product
* Avoid client-side console logs in production

    _Tại sao vậy:_
    > Mặc dù công việc xử lý build có thể bỏ qua những yếu tố đó, nhưng hãy đảm bảo bộ kiểm tra code style có đưa ra các cảnh báo cho bạn về các console log dư thừa này. 

* Tạo ra các log có thể đọc được. Sử dụng các liblary cho việc log để sử dụng trong chế độ production (ví dụ [winston](https://github.com/winstonjs/winston) hoặc                    [node-bunyan](https://github.com/trentm/node-bunyan))

    _Tại sao vậy:_
    > Điều đó làm cho các vấn đề của bạn bớt khó chịu hơn cùng với những thông tin log, timestamp có màu sắc trong 1 file ngoài và thậm chí ghi log tới 1 file hằng ngày. [xen thêm ...](https://blog.risingstack.com/node-js-logging-tutorial/)

<a name="api"></a>
## 9. API
<a name="api-design"></a>

![API](/images/api.png)

### 9.1 API design

_Tại sao vậy:_
> Because we try to enforce development of sanely constructed RESTful interfaces, which team members and clients can consume simply and consistently.  

_Tại sao vậy:_
> Lack of consistency and simplicity can massively increase integration and maintenance costs. Which is why `API design` is included in this document.


* We mostly follow resource-oriented design. It has three main factors: resources, collection, and URLs.
    * A resource has data, gets nested, and there are methods that operate against it.
    * A group of resources is called a collection.
    * URL identifies the online location of resource or collection.
    
    _Tại sao vậy:_
    > This is a very well-known design to developers (your main API consumers). Apart from readability and ease of use, it allows us to write generic libraries and connectors without even knowing what the API is about.

* use kebab-case for URLs.
* use camelCase for parameters in the query string or resource fields.
* use plural kebab-case for resource names in URLs.

* Always use a plural nouns for naming a url pointing to a collection: `/users`.

    _Tại sao vậy:_
    > Basically, it reads better and keeps URLs consistent. [read more...](https://apigee.com/about/blog/technology/restful-api-design-plural-nouns-and-concrete-names)

* In the source code convert plurals to variables and properties with a List suffix.

    _Tại sao vậy:_
    > Plural is nice in the URL but in the source code, it’s just too subtle and error-prone.

* Always use a singular concept that starts with a collection and ends to an identifier:

    ```
    /students/245743
    /airports/kjfk
    ```
* Avoid URLs like this: 
    ```
    GET /blogs/:blogId/posts/:postId/summary
    ```

    _Tại sao vậy:_
    > This is not pointing to a resource but to a property instead. You can pass the property as a parameter to trim your response.

* Keep verbs out of your resource URLs.

    _Tại sao vậy:_
    > Because if you use a verb for each resource operation you soon will have a huge list of URLs and no consistent pattern which makes it difficult for developers to learn. Plus we use verbs for something else.

* Use verbs for non-resources. In this case, your API doesn't return any resources. Instead, you execute an operation and return the result. These **are not** CRUD (create, retrieve, update, and delete) operations:

    ```
    /translate?text=Hallo
    ```

    _Tại sao vậy:_
    > Because for CRUD we use HTTP methods on `resource` or `collection` URLs. The verbs we were talking about are actually `Controllers`. You usually don't develop many of these. [read more...](https://byrondover.github.io/post/restful-api-guidelines/#controller)

* The request body or response type is JSON then please follow `camelCase` for `JSON` property names to maintain the consistency.
    
    _Tại sao vậy:_
    > This is a JavaScript project guideline, where the programming language for generating and parsing JSON is assumed to be JavaScript.

* Even though a resource is a singular concept that is similar to an object instance or database record, you should not use your `table_name` for a resource name and `column_name` resource property.

    _Tại sao vậy:_
    > Because your intention is to expose Resources, not your database schema details.

* Again, only use nouns in your URL when naming your resources and don’t try to explain their functionality.

    _Tại sao vậy:_
    > Only use nouns in your resource URLs, avoid endpoints like `/addNewUser` or `/updateUser` .  Also avoid sending resource operations as a parameter.

* Explain the CRUD functionalities using HTTP methods:

    _How:_
    > `GET`: To retrieve a representation of a resource.
    
    > `POST`: To create new resources and sub-resources.
   
    > `PUT`: To update existing resources.
    
    > `PATCH`: To update existing resources. It only updates the fields that were supplied, leaving the others alone.
    
    > `DELETE`:	To delete existing resources.


* For nested resources, use the relation between them in the URL. For instance, using `id` to relate an employee to a company.

    _Tại sao vậy:_
    > This is a natural way to make resources explorable.

    _How:_

    > `GET      /schools/2/students	` , should get the list of all students from school 2.

    > `GET      /schools/2/students/31`	, should get the details of student 31, which belongs to school 2.

    > `DELETE   /schools/2/students/31`	, should delete student 31, which belongs to school 2.

    > `PUT      /schools/2/students/31`	, should update info of student 31, Use PUT on resource-URL only, not collection.

    > `POST     /schools` , should create a new school and return the details of the new school created. Use POST on collection-URLs.

* Use a simple ordinal number for a version with a `v` prefix (v1, v2). Move it all the way to the left in the URL so that it has the highest scope:
    ```
    http://api.domain.com/v1/schools/3/students	
    ```

    _Tại sao vậy:_
    > When your APIs are public for other third parties, upgrading the APIs with some breaking change would also lead to breaking the existing products or services using your APIs. Using versions in your URL can prevent that from happening. [read more...](https://apigee.com/about/blog/technology/restful-api-design-tips-versioning)



* Response messages must be self-descriptive. A good error message response might look something like this:
    ```json
    {
        "code": 1234,
        "message" : "Something bad happened",
        "description" : "More details"
    }
    ```
    or for validation errors:
    ```json
    {
        "code" : 2314,
        "message" : "Validation Failed",
        "errors" : [
            {
                "code" : 1233,
                "field" : "email",
                "message" : "Invalid email"
            },
            {
                "code" : 1234,
                "field" : "password",
                "message" : "No password provided"
            }
          ]
    }
    ```

    _Tại sao vậy:_
    > developers depend on well-designed errors at the critical times when they are troubleshooting and resolving issues after the applications they've built using your APIs are in the hands of their users.


    _Note: Keep security exception messages as generic as possible. For instance, Instead of saying ‘incorrect password’, you can reply back saying ‘invalid username or password’ so that we don’t unknowingly inform user that username was indeed correct and only the password was incorrect._

* Use these status codes to send with your response to describe whether **everything worked**,
The **client app did something wrong** or The **API did something wrong**.
    
    _Which ones:_
    > `200 OK` response represents success for `GET`, `PUT` or `POST` requests.

    > `201 Created` for when a new instance is created. Creating a new instance, using `POST` method returns `201` status code.

    > `204 No Content` response represents success but there is no content to be sent in the response. Use it when `DELETE` operation succeeds.

    > `304 Not Modified` response is to minimize information transfer when the recipient already has cached representations.

    > `400 Bad Request` for when the request was not processed, as the server could not understand what the client is asking for.

    > `401 Unauthorized` for when the request lacks valid credentials and it should re-request with the required credentials.

    > `403 Forbidden` means the server understood the request but refuses to authorize it.

    > `404 Not Found` indicates that the requested resource was not found. 

    > `500 Internal Server Error` indicates that the request is valid, but the server could not fulfill it due to some unexpected condition.

    _Tại sao vậy:_
    > Most API providers use a small subset HTTP status codes. For example, the Google GData API uses only 10 status codes, Netflix uses 9, and Digg, only 8. Of course, these responses contain a body with additional information.There are over 70 HTTP status codes. However, most developers don't have all 70 memorized. So if you choose status codes that are not very common you will force application developers away from building their apps and over to wikipedia to figure out what you're trying to tell them. [read more...](https://apigee.com/about/blog/technology/restful-api-design-what-about-errors)


* Provide total numbers of resources in your response.
* Accept `limit` and `offset` parameters.

* The amount of data the resource exposes should also be taken into account. The API consumer doesn't always need the full representation of a resource. Use a fields query parameter that takes a comma separated list of fields to include:
    ```
    GET /student?fields=id,name,age,class
    ```
* Pagination, filtering, and sorting don’t need to be supported from start for all resources. Document those resources that offer filtering and sorting.

<a name="api-security"></a>
### 9.2 API security
These are some basic security best practices:

* Don't use basic authentication unless over a secure connection (HTTPS). Authentication tokens must not be transmitted in the URL: `GET /users/123?token=asdf....`

    _Tại sao vậy:_
    > Because Token, or user ID and password are passed over the network as clear text (it is base64 encoded, but base64 is a reversible encoding), the basic authentication scheme is not secure. [read more...](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

* Tokens must be transmitted using the Authorization header on every request: `Authorization: Bearer xxxxxx, Extra yyyyy`.

* Authorization Code should be short-lived.

* Reject any non-TLS requests by not responding to any HTTP request to avoid any insecure data exchange. Respond to HTTP requests by `403 Forbidden`.

* Consider using Rate Limiting.

    _Tại sao vậy:_
    > To protect your APIs from bot threats that call your API thousands of times per hour. You should consider implementing rate limit early on.

* Setting HTTP headers appropriately can help to lock down and secure your web application. [read more...](https://github.com/helmetjs/helmet)

* Your API should convert the received data to their canonical form or reject them. Return 400 Bad Request with details about any errors from bad or missing data.

* All the data exchanged with the REST API must be validated by the API.

* Serialize your JSON.

    _Tại sao vậy:_
    > A key concern with JSON encoders is preventing arbitrary JavaScript remote code execution within the browser... or, if you're using node.js, on the server. It's vital that you use a proper JSON serializer to encode user-supplied data properly to prevent the execution of user-supplied input on the browser.

* Validate the content-type and mostly use `application/*json` (Content-Type header).
    
    _Tại sao vậy:_
    > For instance, accepting the `application/x-www-form-urlencoded` mime type allows the attacker to create a form and trigger a simple POST request. The server should never assume the Content-Type. A lack of Content-Type header or an unexpected Content-Type header should result in the server rejecting the content with a `4XX` response.

* Check the API Security Checklist Project. [read more...](https://github.com/shieldfy/API-Security-Checklist)

<a name="api-documentation"></a>
### 9.3 API documentation
* Fill the `API Reference` section in [README.md template](./README.sample.md) for API.
* Describe API authentication methods with a code sample.
* Explaining The URL Structure (path only, no root URL) including The request type (Method).

For each endpoint explain:
* URL Params If URL Params exist, specify them in accordance with name mentioned in URL section:

    ```
    Required: id=[integer]
    Optional: photo_id=[alphanumeric]
    ```

* If the request type is POST, provide working examples. URL Params rules apply here too. Separate the section into Optional and Required.

* Success Response, What should be the status code and is there any return data? This is useful when people need to know what their callbacks should expect:

    ```
    Code: 200
    Content: { id : 12 }
    ```

* Error Response, Most endpoints have many ways to fail. From unauthorized access to wrongful parameters etc. All of those should be listed here. It might seem repetitive, but it helps prevent assumptions from being made. For example
    ```json
    {
        "code": 403,
        "message" : "Authentication failed",
        "description" : "Invalid username or password"
    }   
    ```


* Use API design tools, There are lots of open source tools for good documentation such as [API Blueprint](https://apiblueprint.org/) and [Swagger](https://swagger.io/).

<a name="licensing"></a>
## 10. Licensing
![Licensing](/images/licensing.png)

Make sure you use resources that you have the rights to use. If you use libraries, remember to look for MIT, Apache or BSD but if you modify them, then take a look at the license details. Copyrighted images and videos may cause legal problems.


---
Sources:
[RisingStack Engineering](https://blog.risingstack.com/),
[Mozilla Developer Network](https://developer.mozilla.org/),
[Heroku Dev Center](https://devcenter.heroku.com),
[Airbnb/javascript](https://github.com/airbnb/javascript),
[Atlassian Git tutorials](https://www.atlassian.com/git/tutorials),
[Apigee](https://apigee.com/about/blog),
[Wishtack](https://blog.wishtack.com)

Icons by [icons8](https://icons8.com/)

