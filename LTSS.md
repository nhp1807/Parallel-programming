# Lập trình song song

> Hệ thống sẽ bao gồm 1 máy master và 3 máy slaves

## Các bước cài đặt
### Bước 1: Cài đặt và thiết lập ssh
- Cập nhật các máy trong hệ thống bằng cách thực hiện các câu lệnh
```ssh
sudo apt update
sudo apt upgrade
```
- Sau khi chạy xong, các máy trong  hệ thống đã được cập nhật
- Tiếp theo ta cần phải lấy được địa chỉ IP của mỗi máy
```ssh
sudo apt install tool-nets
```
- Sau khi cài đặt, chúng ta thực hiện lấy IP của máy bằng cách thực hiện câu lệnh
```ssh
ifconfig
```
- Thực hiện cài đặt ssh server và ssh client trên các máy
```ssh
sudo apt install openssh-server
sudo apt install openssh-client
```
- Sau khi cài đăt ssh, ta thực hiện sinh khóa rsa bằng cách thực hiện câu lệnh

```ssh
ssh-keygen -t rsa
```

- Nhập folder muốn lưu trữ khóa, nếu muốn để mặc định thì ấn Enter
- Nhập mật khẩu khóa (không bắt buộc)
- Sao chép các file id_rsa_xxx.pub của các slave vào folder ~/.ssh/ vào máy master
- Sao chép file id_rsa_master.pub của máy master vào folder ~/.ssh/ vào các máy slaves
- Sau khi đã sao chép vào, mỗi máy slave sẽ có 3 files và máy master sẽ có 5 files
- Trong máy master, thực hiện các câu lệnh

```ssh
cat /home/mpiuser/.ssh/id_rsa_slaves1.pub >> /home/mpiuser/.ssh/authorized_keys
cat /home/mpiuser/.ssh/id_rsa_slaves2.pub >> /home/mpiuser/.ssh/authorized_keys
cat /home/mpiuser/.ssh/id_rsa_slaves3.pub >> /home/mpiuser/.ssh/authorized_keys
```

- Đối với các máy slave, thực hiện câu lệnh
```ssh
cat /home/mpiuser/.ssh/id_rsa_master.pub >> /home/mpiuser/.ssh/authorized_keys
```

- Sử dụng trình soạn thảo văn bản bất kỳ (nano, gedit, vi, ...) để thực hiện thay đổi file config trong đường dẫn /etc/ssh/sshd_config
- Thêm 2 dòng sau vào trong file và lưu lại

```ssh
PubkeyAuthentication yes
RSAAuthentication yes
```

- Sau khi thực hiện, các máy restart lại ssh 

```ssh
service ssh restart
```

- Tiếp theo ta thực hiện ssh vào các máy
```ssh
ssh mpiuser@<ip_address>
```
- Ta nhập mật khẩu của user và mật khẩu khóa (nếu cần)

### Bước 2: 

- Import a HTML file and watch it magically convert to Markdown
- Drag and drop images (requires your Dropbox account be linked)
- Import and save files from GitHub, Dropbox, Google Drive and One Drive
- Drag and drop markdown and HTML files into Dillinger
- Export documents as Markdown, HTML and PDF

Markdown is a lightweight markup language based on the formatting conventions
that people naturally use in email.
As [John Gruber] writes on the [Markdown site][df1]


This text you see here is *actually- written in Markdown! To get a feel
for Markdown's syntax, type some text into the left window and
watch the results in the right.

## Tech

Dillinger uses a number of open source projects to work properly:

- [AngularJS] - HTML enhanced for web apps!
- [Ace Editor] - awesome web-based text editor
- [markdown-it] - Markdown parser done right. Fast and easy to extend.
- [Twitter Bootstrap] - great UI boilerplate for modern web apps
- [node.js] - evented I/O for the backend
- [Express] - fast node.js network app framework [@tjholowaychuk]
- [Gulp] - the streaming build system
- [Breakdance](https://breakdance.github.io/breakdance/) - HTML
to Markdown converter
- [jQuery] - duh

And of course Dillinger itself is open source with a [public repository][dill]
 on GitHub.

## Installation

Dillinger requires [Node.js](https://nodejs.org/) v10+ to run.

Install the dependencies and devDependencies and start the server.

```sh
cd dillinger
npm i
node app
```

For production environments...

```sh
npm install --production
NODE_ENV=production node app
```

## Plugins

Dillinger is currently extended with the following plugins.
Instructions on how to use them in your own application are linked below.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Development

Want to contribute? Great!

Dillinger uses Gulp + Webpack for fast developing.
Make a change in your file and instantaneously see your updates!

Open your favorite Terminal and run these commands.

First Tab:

```sh
node app
```

Second Tab:

```sh
gulp watch
```

(optional) Third:

```sh
karma test
```

#### Building for source

For production release:

```sh
gulp build --prod
```

Generating pre-built zip archives for distribution:

```sh
gulp build dist --prod
```

## Docker

Dillinger is very easy to install and deploy in a Docker container.

By default, the Docker will expose port 8080, so change this within the
Dockerfile if necessary. When ready, simply use the Dockerfile to
build the image.

```sh
cd dillinger
docker build -t <youruser>/dillinger:${package.json.version} .
```

This will create the dillinger image and pull in the necessary dependencies.
Be sure to swap out `${package.json.version}` with the actual
version of Dillinger.

Once done, run the Docker image and map the port to whatever you wish on
your host. In this example, we simply map port 8000 of the host to
port 8080 of the Docker (or whatever port was exposed in the Dockerfile):

```sh
docker run -d -p 8000:8080 --restart=always --cap-add=SYS_ADMIN --name=dillinger <youruser>/dillinger:${package.json.version}
```

> Note: `--capt-add=SYS-ADMIN` is required for PDF rendering.

Verify the deployment by navigating to your server address in
your preferred browser.

```sh
127.0.0.1:8000
```

## License

MIT

**Free Software, Hell Yeah!**

[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)

   [dill]: <https://github.com/joemccann/dillinger>
   [git-repo-url]: <https://github.com/joemccann/dillinger.git>
   [john gruber]: <http://daringfireball.net>
   [df1]: <http://daringfireball.net/projects/markdown/>
   [markdown-it]: <https://github.com/markdown-it/markdown-it>
   [Ace Editor]: <http://ace.ajax.org>
   [node.js]: <http://nodejs.org>
   [Twitter Bootstrap]: <http://twitter.github.com/bootstrap/>
   [jQuery]: <http://jquery.com>
   [@tjholowaychuk]: <http://twitter.com/tjholowaychuk>
   [express]: <http://expressjs.com>
   [AngularJS]: <http://angularjs.org>
   [Gulp]: <http://gulpjs.com>

   [PlDb]: <https://github.com/joemccann/dillinger/tree/master/plugins/dropbox/README.md>
   [PlGh]: <https://github.com/joemccann/dillinger/tree/master/plugins/github/README.md>
   [PlGd]: <https://github.com/joemccann/dillinger/tree/master/plugins/googledrive/README.md>
   [PlOd]: <https://github.com/joemccann/dillinger/tree/master/plugins/onedrive/README.md>
   [PlMe]: <https://github.com/joemccann/dillinger/tree/master/plugins/medium/README.md>
   [PlGa]: <https://github.com/RahulHP/dillinger/blob/master/plugins/googleanalytics/README.md>
