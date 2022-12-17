# Домашнее задание к занятию "3. Использование Yandex Cloud"

## Подготовка к выполнению

1. Подготовьте в Yandex Cloud три хоста: для `clickhouse`, для `vector` и для `lighthouse`.

Ссылка на репозиторий LightHouse: https://github.com/VKCOM/lighthouse

### Ответ

Инфрастуктура разворачивалась с помощью Terraform 
[Terraform](./terraform)


## Основная часть

1. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает lighthouse.
2. При создании tasks рекомендую использовать модули: `get_url`, `template`, `yum`, `apt`.
3. Tasks должны: скачать статику lighthouse, установить nginx или любой другой webserver, настроить его конфиг для открытия lighthouse, запустить webserver.
4. Приготовьте свой собственный inventory файл `prod.yml`.

5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.

### Ответ

Ошибки исправлены
```bash
root@vkvm:/home/vk/DZ8.3/playbook# ansible-lint site.yml 
WARNING  Overriding detected file kind 'yaml' with 'playbook' for given positional argument: site.yml
```

6. Попробуйте запустить playbook на этом окружении с флагом `--check`.

### Ответ

```bash
root@vkvm:/home/vk/DZ8.3/playbook# ansible-playbook --check -i inventory/prod.yml site.yml
root@vkvm:/home/vk/DZ8.3_NEW/playbook# ansible-playbook --check -i inventory/prod.yml site.yml

PLAY [Install Clickhouse] **************************************************************************************

TASK [Gathering Facts] *****************************************************************************************
The authenticity of host '158.160.53.8 (158.160.53.8)' can't be established.
ED25519 key fingerprint is SHA256:Te9VemIBZMmp4p3a6/GOFXupkInDiGZijgowVl72ShQ.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
ok: [clickhouse-01]

TASK [Get clickhouse distrib] **********************************************************************************
changed: [clickhouse-01] => (item=clickhouse-client)
changed: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "item": "clickhouse-common-static", "msg": "Request failed", "response": "HTTP Error 404: Not Found", "status_code": 404, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] **********************************************************************************
changed: [clickhouse-01]

TASK [Install clickhouse packages] *****************************************************************************
fatal: [clickhouse-01]: FAILED! => {"changed": false, "msg": "No RPM file matching 'clickhouse-common-static-22.3.3.44.rpm' found on system", "rc": 127, "results": ["No RPM file matching 'clickhouse-common-static-22.3.3.44.rpm' found on system"]}

PLAY RECAP *****************************************************************************************************
clickhouse-01              : ok=2    changed=1    unreachable=0    failed=1    skipped=0    rescued=1    ignored=0   
```

7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.

### Ответ

```bash
root@vkvm:/home/vk/misc/ansible/08-ansible-03-yandex/playbook# ansible-playbook --diff -i inventory/prod.yml site.yml 
```
<details><summary>Click to open</summary>
PLAY [Install Clickhouse] **************************************************************************************

TASK [Gathering Facts] *****************************************************************************************
The authenticity of host '158.160.53.8 (158.160.53.8)' can't be established.
ED25519 key fingerprint is SHA256:Te9VemIBZMmp4p3a6/GOFXupkInDiGZijgowVl72ShQ.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
ok: [clickhouse-01]

TASK [Get clickhouse distrib] **********************************************************************************
changed: [clickhouse-01] => (item=clickhouse-client)
changed: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "item": "clickhouse-common-static", "msg": "Request failed", "response": "HTTP Error 404: Not Found", "status_code": 404, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] **********************************************************************************
changed: [clickhouse-01]

TASK [Install clickhouse packages] *****************************************************************************
fatal: [clickhouse-01]: FAILED! => {"changed": false, "msg": "No RPM file matching 'clickhouse-common-static-22.3.3.44.rpm' found on system", "rc": 127, "results": ["No RPM file matching 'clickhouse-common-static-22.3.3.44.rpm' found on system"]}

PLAY RECAP *****************************************************************************************************
clickhouse-01              : ok=2    changed=1    unreachable=0    failed=1    skipped=0    rescued=1    ignored=0   

root@vkvm:/home/vk/DZ8.3_NEW/playbook# ansible-playbook --diff -i inventory/prod.yml site.yml 

PLAY [Install Clickhouse] **************************************************************************************

TASK [Gathering Facts] *****************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] **********************************************************************************
changed: [clickhouse-01] => (item=clickhouse-client)
changed: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "item": "clickhouse-common-static", "msg": "Request failed", "response": "HTTP Error 404: Not Found", "status_code": 404, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] **********************************************************************************
changed: [clickhouse-01]

TASK [Install clickhouse packages] *****************************************************************************
changed: [clickhouse-01]

TASK [Start clickhouse service] ********************************************************************************
changed: [clickhouse-01]

TASK [Create database] *****************************************************************************************
changed: [clickhouse-01]

RUNNING HANDLER [Start clickhouse service] *********************************************************************
changed: [clickhouse-01]

PLAY [Install Vector] ******************************************************************************************

TASK [Gathering Facts] *****************************************************************************************
The authenticity of host '158.160.46.148 (158.160.46.148)' can't be established.
ED25519 key fingerprint is SHA256:/+V0gP9dGhnnJlRf818Z1RqH300uUP9AV+lYO9lGfe8.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
ok: [vector-01]

TASK [Get vector distrib] **************************************************************************************
changed: [vector-01]

TASK [Install vector packages] *********************************************************************************
Selecting previously unselected package vector.
(Reading database ... 73800 files and directories currently installed.)
Preparing to unpack ./vector-0.25.2.dpkg ...
Unpacking vector (0.25.2-1) ...
Setting up vector (0.25.2-1) ...
systemd-journal:x:101:
changed: [vector-01]

RUNNING HANDLER [start vector service] *************************************************************************
changed: [vector-01]

PLAY [Install nginx] *******************************************************************************************

TASK [Gathering Facts] *****************************************************************************************
The authenticity of host '158.160.52.200 (158.160.52.200)' can't be established.
ED25519 key fingerprint is SHA256:mdQZJNlZE89WLQIJ6R+pWKK+m62ZBX86MayfeOFkw+M.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
ok: [lighthouse-01]

TASK [Install nginx] *******************************************************************************************
The following packages were automatically installed and are no longer required:
  libflashrom1 libftdi1-2
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  fontconfig-config fonts-dejavu-core libdeflate0 libfontconfig1 libgd3
  libjbig0 libjpeg-turbo8 libjpeg8 libnginx-mod-http-geoip2
  libnginx-mod-http-image-filter libnginx-mod-http-xslt-filter
  libnginx-mod-mail libnginx-mod-stream libnginx-mod-stream-geoip2 libtiff5
  libwebp7 libxpm4 nginx-common nginx-core
Suggested packages:
  libgd-tools fcgiwrap nginx-doc ssl-cert
The following NEW packages will be installed:
  fontconfig-config fonts-dejavu-core libdeflate0 libfontconfig1 libgd3
  libjbig0 libjpeg-turbo8 libjpeg8 libnginx-mod-http-geoip2
  libnginx-mod-http-image-filter libnginx-mod-http-xslt-filter
  libnginx-mod-mail libnginx-mod-stream libnginx-mod-stream-geoip2 libtiff5
  libwebp7 libxpm4 nginx nginx-common nginx-core
0 upgraded, 20 newly installed, 0 to remove and 8 not upgraded.
changed: [lighthouse-01]

RUNNING HANDLER [restart nginx service] ************************************************************************
changed: [lighthouse-01]

PLAY [Install lighthouse] **************************************************************************************

TASK [Gathering Facts] *****************************************************************************************
ok: [lighthouse-01]

TASK [install unzip] *******************************************************************************************
The following packages were automatically installed and are no longer required:
  libflashrom1 libftdi1-2
Use 'sudo apt autoremove' to remove them.
Suggested packages:
  zip
The following NEW packages will be installed:
  unzip
0 upgraded, 1 newly installed, 0 to remove and 8 not upgraded.
changed: [lighthouse-01]

TASK [Get lighthouse distrib] **********************************************************************************
changed: [lighthouse-01]

TASK [Unarchive lighthouse distrib into nginx] *****************************************************************
>d++++++.?? lighthouse-master/
>f++++++.?? lighthouse-master/LICENSE
>f++++++.?? lighthouse-master/README.md
>f++++++.?? lighthouse-master/app.js
>d++++++.?? lighthouse-master/css/
>f++++++.?? lighthouse-master/css/bootstrap-responsive.css
>f++++++.?? lighthouse-master/css/bootstrap-responsive.min.css
>f++++++.?? lighthouse-master/css/bootstrap.css
>f++++++.?? lighthouse-master/css/bootstrap.min.css
>d++++++.?? lighthouse-master/img/
>f++++++.?? lighthouse-master/img/glyphicons-halflings-white.png
>f++++++.?? lighthouse-master/img/glyphicons-halflings.png
>f++++++.?? lighthouse-master/img/loading.svg
>f++++++.?? lighthouse-master/index.html
>f++++++.?? lighthouse-master/jquery.js
>d++++++.?? lighthouse-master/js/
>d++++++.?? lighthouse-master/js/ace-min/
>f++++++.?? lighthouse-master/js/ace-min/ace.js
>f++++++.?? lighthouse-master/js/ace-min/ch_completions_help.js
>f++++++.?? lighthouse-master/js/ace-min/clickhouse_highlight_rules.js
>f++++++.?? lighthouse-master/js/ace-min/ext-beautify.js
>f++++++.?? lighthouse-master/js/ace-min/ext-elastic_tabstops_lite.js
>f++++++.?? lighthouse-master/js/ace-min/ext-emmet.js
>f++++++.?? lighthouse-master/js/ace-min/ext-error_marker.js
>f++++++.?? lighthouse-master/js/ace-min/ext-keybinding_menu.js
>f++++++.?? lighthouse-master/js/ace-min/ext-language_tools.js
>f++++++.?? lighthouse-master/js/ace-min/ext-linking.js
>f++++++.?? lighthouse-master/js/ace-min/ext-modelist.js
>f++++++.?? lighthouse-master/js/ace-min/ext-options.js
>f++++++.?? lighthouse-master/js/ace-min/ext-searchbox.js
>f++++++.?? lighthouse-master/js/ace-min/ext-settings_menu.js
>f++++++.?? lighthouse-master/js/ace-min/ext-spellcheck.js
>f++++++.?? lighthouse-master/js/ace-min/ext-split.js
>f++++++.?? lighthouse-master/js/ace-min/ext-static_highlight.js
>f++++++.?? lighthouse-master/js/ace-min/ext-statusbar.js
>f++++++.?? lighthouse-master/js/ace-min/ext-textarea.js
>f++++++.?? lighthouse-master/js/ace-min/ext-themelist.js
>f++++++.?? lighthouse-master/js/ace-min/ext-whitespace.js
>f++++++.?? lighthouse-master/js/ace-min/keybinding-emacs.js
>f++++++.?? lighthouse-master/js/ace-min/keybinding-vim.js
>f++++++.?? lighthouse-master/js/ace-min/mode-abap.js
>f++++++.?? lighthouse-master/js/ace-min/mode-abc.js
>f++++++.?? lighthouse-master/js/ace-min/mode-actionscript.js
>f++++++.?? lighthouse-master/js/ace-min/mode-ada.js
>f++++++.?? lighthouse-master/js/ace-min/mode-apache_conf.js
>f++++++.?? lighthouse-master/js/ace-min/mode-applescript.js
>f++++++.?? lighthouse-master/js/ace-min/mode-asciidoc.js
>f++++++.?? lighthouse-master/js/ace-min/mode-assembly_x86.js
>f++++++.?? lighthouse-master/js/ace-min/mode-autohotkey.js
>f++++++.?? lighthouse-master/js/ace-min/mode-batchfile.js
>f++++++.?? lighthouse-master/js/ace-min/mode-bro.js
>f++++++.?? lighthouse-master/js/ace-min/mode-c9search.js
>f++++++.?? lighthouse-master/js/ace-min/mode-c_cpp.js
>f++++++.?? lighthouse-master/js/ace-min/mode-cirru.js
>f++++++.?? lighthouse-master/js/ace-min/mode-clickhouse.js
>f++++++.?? lighthouse-master/js/ace-min/mode-clojure.js
>f++++++.?? lighthouse-master/js/ace-min/mode-cobol.js
>f++++++.?? lighthouse-master/js/ace-min/mode-coffee.js
>f++++++.?? lighthouse-master/js/ace-min/mode-coldfusion.js
>f++++++.?? lighthouse-master/js/ace-min/mode-csharp.js
>f++++++.?? lighthouse-master/js/ace-min/mode-csound_document.js
>f++++++.?? lighthouse-master/js/ace-min/mode-csound_orchestra.js
>f++++++.?? lighthouse-master/js/ace-min/mode-csound_score.js
>f++++++.?? lighthouse-master/js/ace-min/mode-csp.js
>f++++++.?? lighthouse-master/js/ace-min/mode-css.js
>f++++++.?? lighthouse-master/js/ace-min/mode-curly.js
>f++++++.?? lighthouse-master/js/ace-min/mode-d.js
>f++++++.?? lighthouse-master/js/ace-min/mode-dart.js
>f++++++.?? lighthouse-master/js/ace-min/mode-diff.js
>f++++++.?? lighthouse-master/js/ace-min/mode-django.js
>f++++++.?? lighthouse-master/js/ace-min/mode-dockerfile.js
>f++++++.?? lighthouse-master/js/ace-min/mode-dot.js
>f++++++.?? lighthouse-master/js/ace-min/mode-drools.js
>f++++++.?? lighthouse-master/js/ace-min/mode-edifact.js
>f++++++.?? lighthouse-master/js/ace-min/mode-eiffel.js
>f++++++.?? lighthouse-master/js/ace-min/mode-ejs.js
>f++++++.?? lighthouse-master/js/ace-min/mode-elixir.js
>f++++++.?? lighthouse-master/js/ace-min/mode-elm.js
>f++++++.?? lighthouse-master/js/ace-min/mode-erlang.js
>f++++++.?? lighthouse-master/js/ace-min/mode-forth.js
>f++++++.?? lighthouse-master/js/ace-min/mode-fortran.js
>f++++++.?? lighthouse-master/js/ace-min/mode-ftl.js
>f++++++.?? lighthouse-master/js/ace-min/mode-gcode.js
>f++++++.?? lighthouse-master/js/ace-min/mode-gherkin.js
>f++++++.?? lighthouse-master/js/ace-min/mode-gitignore.js
>f++++++.?? lighthouse-master/js/ace-min/mode-glsl.js
>f++++++.?? lighthouse-master/js/ace-min/mode-gobstones.js
>f++++++.?? lighthouse-master/js/ace-min/mode-golang.js
>f++++++.?? lighthouse-master/js/ace-min/mode-graphqlschema.js
>f++++++.?? lighthouse-master/js/ace-min/mode-groovy.js
>f++++++.?? lighthouse-master/js/ace-min/mode-haml.js
>f++++++.?? lighthouse-master/js/ace-min/mode-handlebars.js
>f++++++.?? lighthouse-master/js/ace-min/mode-haskell.js
>f++++++.?? lighthouse-master/js/ace-min/mode-haskell_cabal.js
>f++++++.?? lighthouse-master/js/ace-min/mode-haxe.js
>f++++++.?? lighthouse-master/js/ace-min/mode-hjson.js
>f++++++.?? lighthouse-master/js/ace-min/mode-html.js
>f++++++.?? lighthouse-master/js/ace-min/mode-html_elixir.js
>f++++++.?? lighthouse-master/js/ace-min/mode-html_ruby.js
>f++++++.?? lighthouse-master/js/ace-min/mode-ini.js
>f++++++.?? lighthouse-master/js/ace-min/mode-io.js
>f++++++.?? lighthouse-master/js/ace-min/mode-jack.js
>f++++++.?? lighthouse-master/js/ace-min/mode-jade.js
>f++++++.?? lighthouse-master/js/ace-min/mode-java.js
>f++++++.?? lighthouse-master/js/ace-min/mode-javascript.js
>f++++++.?? lighthouse-master/js/ace-min/mode-json.js
>f++++++.?? lighthouse-master/js/ace-min/mode-jsoniq.js
>f++++++.?? lighthouse-master/js/ace-min/mode-jsp.js
>f++++++.?? lighthouse-master/js/ace-min/mode-jssm.js
>f++++++.?? lighthouse-master/js/ace-min/mode-jsx.js
>f++++++.?? lighthouse-master/js/ace-min/mode-julia.js
>f++++++.?? lighthouse-master/js/ace-min/mode-kotlin.js
>f++++++.?? lighthouse-master/js/ace-min/mode-latex.js
>f++++++.?? lighthouse-master/js/ace-min/mode-less.js
>f++++++.?? lighthouse-master/js/ace-min/mode-liquid.js
>f++++++.?? lighthouse-master/js/ace-min/mode-lisp.js
>f++++++.?? lighthouse-master/js/ace-min/mode-livescript.js
>f++++++.?? lighthouse-master/js/ace-min/mode-logiql.js
>f++++++.?? lighthouse-master/js/ace-min/mode-lsl.js
>f++++++.?? lighthouse-master/js/ace-min/mode-lua.js
>f++++++.?? lighthouse-master/js/ace-min/mode-luapage.js
>f++++++.?? lighthouse-master/js/ace-min/mode-lucene.js
>f++++++.?? lighthouse-master/js/ace-min/mode-makefile.js
>f++++++.?? lighthouse-master/js/ace-min/mode-markdown.js
>f++++++.?? lighthouse-master/js/ace-min/mode-mask.js
>f++++++.?? lighthouse-master/js/ace-min/mode-matlab.js
>f++++++.?? lighthouse-master/js/ace-min/mode-maze.js
>f++++++.?? lighthouse-master/js/ace-min/mode-mel.js
>f++++++.?? lighthouse-master/js/ace-min/mode-mixal.js
>f++++++.?? lighthouse-master/js/ace-min/mode-mushcode.js
>f++++++.?? lighthouse-master/js/ace-min/mode-mysql.js
>f++++++.?? lighthouse-master/js/ace-min/mode-nix.js
>f++++++.?? lighthouse-master/js/ace-min/mode-nsis.js
>f++++++.?? lighthouse-master/js/ace-min/mode-objectivec.js
>f++++++.?? lighthouse-master/js/ace-min/mode-ocaml.js
>f++++++.?? lighthouse-master/js/ace-min/mode-pascal.js
>f++++++.?? lighthouse-master/js/ace-min/mode-perl.js
>f++++++.?? lighthouse-master/js/ace-min/mode-pgsql.js
>f++++++.?? lighthouse-master/js/ace-min/mode-php.js
>f++++++.?? lighthouse-master/js/ace-min/mode-pig.js
>f++++++.?? lighthouse-master/js/ace-min/mode-plain_text.js
>f++++++.?? lighthouse-master/js/ace-min/mode-powershell.js
>f++++++.?? lighthouse-master/js/ace-min/mode-praat.js
>f++++++.?? lighthouse-master/js/ace-min/mode-prolog.js
>f++++++.?? lighthouse-master/js/ace-min/mode-properties.js
>f++++++.?? lighthouse-master/js/ace-min/mode-protobuf.js
>f++++++.?? lighthouse-master/js/ace-min/mode-python.js
>f++++++.?? lighthouse-master/js/ace-min/mode-r.js
>f++++++.?? lighthouse-master/js/ace-min/mode-razor.js
>f++++++.?? lighthouse-master/js/ace-min/mode-rdoc.js
>f++++++.?? lighthouse-master/js/ace-min/mode-red.js
>f++++++.?? lighthouse-master/js/ace-min/mode-redshift.js
>f++++++.?? lighthouse-master/js/ace-min/mode-rhtml.js
>f++++++.?? lighthouse-master/js/ace-min/mode-rst.js
>f++++++.?? lighthouse-master/js/ace-min/mode-ruby.js
>f++++++.?? lighthouse-master/js/ace-min/mode-rust.js
>f++++++.?? lighthouse-master/js/ace-min/mode-sass.js
>f++++++.?? lighthouse-master/js/ace-min/mode-scad.js
>f++++++.?? lighthouse-master/js/ace-min/mode-scala.js
>f++++++.?? lighthouse-master/js/ace-min/mode-scheme.js
>f++++++.?? lighthouse-master/js/ace-min/mode-scss.js
>f++++++.?? lighthouse-master/js/ace-min/mode-sh.js
>f++++++.?? lighthouse-master/js/ace-min/mode-sjs.js
>f++++++.?? lighthouse-master/js/ace-min/mode-smarty.js
>f++++++.?? lighthouse-master/js/ace-min/mode-snippets.js
>f++++++.?? lighthouse-master/js/ace-min/mode-soy_template.js
>f++++++.?? lighthouse-master/js/ace-min/mode-space.js
>f++++++.?? lighthouse-master/js/ace-min/mode-sparql.js
>f++++++.?? lighthouse-master/js/ace-min/mode-sql.js
>f++++++.?? lighthouse-master/js/ace-min/mode-sqlserver.js
>f++++++.?? lighthouse-master/js/ace-min/mode-stylus.js
>f++++++.?? lighthouse-master/js/ace-min/mode-svg.js
>f++++++.?? lighthouse-master/js/ace-min/mode-swift.js
>f++++++.?? lighthouse-master/js/ace-min/mode-tcl.js
>f++++++.?? lighthouse-master/js/ace-min/mode-tex.js
>f++++++.?? lighthouse-master/js/ace-min/mode-text.js
>f++++++.?? lighthouse-master/js/ace-min/mode-textile.js
>f++++++.?? lighthouse-master/js/ace-min/mode-toml.js
>f++++++.?? lighthouse-master/js/ace-min/mode-tsx.js
>f++++++.?? lighthouse-master/js/ace-min/mode-turtle.js
>f++++++.?? lighthouse-master/js/ace-min/mode-twig.js
>f++++++.?? lighthouse-master/js/ace-min/mode-typescript.js
>f++++++.?? lighthouse-master/js/ace-min/mode-vala.js
>f++++++.?? lighthouse-master/js/ace-min/mode-vbscript.js
>f++++++.?? lighthouse-master/js/ace-min/mode-velocity.js
>f++++++.?? lighthouse-master/js/ace-min/mode-verilog.js
>f++++++.?? lighthouse-master/js/ace-min/mode-vhdl.js
>f++++++.?? lighthouse-master/js/ace-min/mode-wollok.js
>f++++++.?? lighthouse-master/js/ace-min/mode-xml.js
>f++++++.?? lighthouse-master/js/ace-min/mode-xquery.js
>f++++++.?? lighthouse-master/js/ace-min/mode-yaml.js
>d++++++.?? lighthouse-master/js/ace-min/snippets/
>f++++++.?? lighthouse-master/js/ace-min/snippets/abap.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/abc.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/actionscript.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/ada.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/apache_conf.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/applescript.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/asciidoc.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/assembly_x86.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/autohotkey.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/batchfile.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/bro.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/c9search.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/c_cpp.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/cirru.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/clickhouse.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/clojure.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/cobol.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/coffee.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/coldfusion.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/csharp.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/csound_document.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/csound_orchestra.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/csound_score.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/csp.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/css.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/curly.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/d.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/dart.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/diff.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/django.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/dockerfile.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/dot.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/drools.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/edifact.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/eiffel.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/ejs.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/elixir.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/elm.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/erlang.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/forth.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/fortran.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/ftl.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/gcode.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/gherkin.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/gitignore.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/glsl.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/gobstones.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/golang.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/graphqlschema.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/groovy.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/haml.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/handlebars.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/haskell.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/haskell_cabal.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/haxe.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/hjson.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/html.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/html_elixir.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/html_ruby.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/ini.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/io.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/jack.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/jade.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/java.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/javascript.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/json.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/jsoniq.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/jsp.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/jssm.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/jsx.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/julia.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/kotlin.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/latex.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/less.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/liquid.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/lisp.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/livescript.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/logiql.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/lsl.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/lua.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/luapage.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/lucene.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/makefile.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/markdown.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/mask.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/matlab.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/maze.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/mel.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/mixal.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/mushcode.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/mysql.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/nix.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/nsis.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/objectivec.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/ocaml.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/pascal.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/perl.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/pgsql.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/php.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/pig.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/plain_text.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/powershell.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/praat.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/prolog.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/properties.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/protobuf.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/python.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/r.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/razor.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/rdoc.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/red.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/redshift.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/rhtml.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/rst.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/ruby.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/rust.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/sass.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/scad.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/scala.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/scheme.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/scss.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/sh.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/sjs.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/smarty.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/snippets.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/soy_template.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/space.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/sparql.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/sql.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/sqlserver.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/stylus.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/svg.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/swift.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/tcl.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/tex.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/text.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/textile.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/toml.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/tsx.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/turtle.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/twig.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/typescript.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/vala.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/vbscript.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/velocity.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/verilog.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/vhdl.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/wollok.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/xml.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/xquery.js
>f++++++.?? lighthouse-master/js/ace-min/snippets/yaml.js
>f++++++.?? lighthouse-master/js/ace-min/theme-ambiance.js
>f++++++.?? lighthouse-master/js/ace-min/theme-chaos.js
>f++++++.?? lighthouse-master/js/ace-min/theme-chrome.js
>f++++++.?? lighthouse-master/js/ace-min/theme-clouds.js
>f++++++.?? lighthouse-master/js/ace-min/theme-clouds_midnight.js
>f++++++.?? lighthouse-master/js/ace-min/theme-cobalt.js
>f++++++.?? lighthouse-master/js/ace-min/theme-crimson_editor.js
>f++++++.?? lighthouse-master/js/ace-min/theme-dawn.js
>f++++++.?? lighthouse-master/js/ace-min/theme-dracula.js
>f++++++.?? lighthouse-master/js/ace-min/theme-dreamweaver.js
>f++++++.?? lighthouse-master/js/ace-min/theme-eclipse.js
>f++++++.?? lighthouse-master/js/ace-min/theme-github.js
>f++++++.?? lighthouse-master/js/ace-min/theme-gob.js
>f++++++.?? lighthouse-master/js/ace-min/theme-gruvbox.js
>f++++++.?? lighthouse-master/js/ace-min/theme-idle_fingers.js
>f++++++.?? lighthouse-master/js/ace-min/theme-iplastic.js
>f++++++.?? lighthouse-master/js/ace-min/theme-katzenmilch.js
>f++++++.?? lighthouse-master/js/ace-min/theme-kr_theme.js
>f++++++.?? lighthouse-master/js/ace-min/theme-kuroir.js
>f++++++.?? lighthouse-master/js/ace-min/theme-merbivore.js
>f++++++.?? lighthouse-master/js/ace-min/theme-merbivore_soft.js
>f++++++.?? lighthouse-master/js/ace-min/theme-mono_industrial.js
>f++++++.?? lighthouse-master/js/ace-min/theme-monokai.js
>f++++++.?? lighthouse-master/js/ace-min/theme-pastel_on_dark.js
>f++++++.?? lighthouse-master/js/ace-min/theme-solarized_dark.js
>f++++++.?? lighthouse-master/js/ace-min/theme-solarized_light.js
>f++++++.?? lighthouse-master/js/ace-min/theme-sqlserver.js
>f++++++.?? lighthouse-master/js/ace-min/theme-terminal.js
>f++++++.?? lighthouse-master/js/ace-min/theme-textmate.js
>f++++++.?? lighthouse-master/js/ace-min/theme-tomorrow.js
>f++++++.?? lighthouse-master/js/ace-min/theme-tomorrow_night.js
>f++++++.?? lighthouse-master/js/ace-min/theme-tomorrow_night_blue.js
>f++++++.?? lighthouse-master/js/ace-min/theme-tomorrow_night_bright.js
>f++++++.?? lighthouse-master/js/ace-min/theme-tomorrow_night_eighties.js
>f++++++.?? lighthouse-master/js/ace-min/theme-twilight.js
>f++++++.?? lighthouse-master/js/ace-min/theme-vibrant_ink.js
>f++++++.?? lighthouse-master/js/ace-min/theme-xcode.js
>f++++++.?? lighthouse-master/js/ace-min/worker-coffee.js
>f++++++.?? lighthouse-master/js/ace-min/worker-css.js
>f++++++.?? lighthouse-master/js/ace-min/worker-html.js
>f++++++.?? lighthouse-master/js/ace-min/worker-javascript.js
>f++++++.?? lighthouse-master/js/ace-min/worker-json.js
>f++++++.?? lighthouse-master/js/ace-min/worker-lua.js
>f++++++.?? lighthouse-master/js/ace-min/worker-php.js
>f++++++.?? lighthouse-master/js/ace-min/worker-xml.js
>f++++++.?? lighthouse-master/js/ace-min/worker-xquery.js
>f++++++.?? lighthouse-master/js/ag-grid.min.js
>f++++++.?? lighthouse-master/js/bootstrap.js
>f++++++.?? lighthouse-master/js/bootstrap.min.js
changed: [lighthouse-01]

TASK [Make nginx config] ***************************************************************************************
--- before: /etc/nginx/sites-enabled/default
+++ after: /root/.ansible/tmp/ansible-local-52243vl_9ct9f/tmp7_q94c9i/default.j2
@@ -1,91 +1,10 @@
-##
-# You should look at the following URL's in order to grasp a solid understanding
-# of Nginx configuration files in order to fully unleash the power of Nginx.
-# https://www.nginx.com/resources/wiki/start/
-# https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/
-# https://wiki.debian.org/Nginx/DirectoryStructure
-#
-# In most cases, administrators will remove this file from sites-enabled/ and
-# leave it as reference inside of sites-available where it will continue to be
-# updated by the nginx packaging team.
-#
-# This file will automatically load configuration files provided by other
-# applications, such as Drupal or Wordpress. These applications will be made
-# available underneath a path with that package name, such as /drupal8.
-#
-# Please see /usr/share/doc/nginx-doc/examples/ for more detailed examples.
-##
-
-# Default server configuration
-#
 server {
-	listen 80 default_server;
-	listen [::]:80 default_server;
-
-	# SSL configuration
-	#
-	# listen 443 ssl default_server;
-	# listen [::]:443 ssl default_server;
-	#
-	# Note: You should disable gzip for SSL traffic.
-	# See: https://bugs.debian.org/773332
-	#
-	# Read up on ssl_ciphers to ensure a secure configuration.
-	# See: https://bugs.debian.org/765782
-	#
-	# Self signed certs generated by the ssl-cert package
-	# Don't use them in a production server!
-	#
-	# include snippets/snakeoil.conf;
-
-	root /var/www/html;
-
-	# Add index.php to the list if you are using PHP
-	index index.html index.htm index.nginx-debian.html;
-
-	server_name _;
-
-	location / {
-		# First attempt to serve request as file, then
-		# as directory, then fall back to displaying a 404.
-		try_files $uri $uri/ =404;
-	}
-
-	# pass PHP scripts to FastCGI server
-	#
-	#location ~ \.php$ {
-	#	include snippets/fastcgi-php.conf;
-	#
-	#	# With php-fpm (or other unix sockets):
-	#	fastcgi_pass unix:/run/php/php7.4-fpm.sock;
-	#	# With php-cgi (or other tcp sockets):
-	#	fastcgi_pass 127.0.0.1:9000;
-	#}
-
-	# deny access to .htaccess files, if Apache's document root
-	# concurs with nginx's one
-	#
-	#location ~ /\.ht {
-	#	deny all;
-	#}
-}
-
-
-# Virtual Host configuration for example.com
-#
-# You can move that to a different file under sites-available/ and symlink that
-# to sites-enabled/ to enable it.
-#
-#server {
-#	listen 80;
-#	listen [::]:80;
-#
-#	server_name example.com;
-#
-#	root /var/www/example.com;
-#	index index.html;
-#
-#	location / {
-#		try_files $uri $uri/ =404;
-#	}
-#}
+        listen 80 default_server;
+        listen [::]:80 default_server;
+        root /var/www/html/lighthouse-master;
+        index index.html index.htm index.nginx-debian.html;
+        server_name _;
+        location / {
+                try_files $uri $uri/ =404;
+        }
+}
\ No newline at end of file

changed: [lighthouse-01]

TASK [Remove lighthouse distrib] *******************************************************************************
--- before
+++ after
@@ -1,4 +1,4 @@
 {
     "path": "./lighthouse.zip",
-    "state": "file"
+    "state": "absent"
 }

changed: [lighthouse-01]
</details>

```bash
RUNNING HANDLER [restart nginx service] ************************************************************************
changed: [lighthouse-01]

PLAY RECAP *****************************************************************************************************
clickhouse-01              : ok=6    changed=5    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
lighthouse-01              : ok=10   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector-01                  : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.

### Ответ

```bash
root@vkvm:/home/vk/DZ8.3_NEW/playbook# ansible-playbook --diff -i inventory/prod.yml site.yml 
```

<details><summary>Click to open</summary>
PLAY [Install Clickhouse] **************************************************************************************

TASK [Gathering Facts] *****************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] **********************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "user", "item": "clickhouse-common-static", "mode": "0664", "msg": "Request failed", "owner": "user", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] **********************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] *****************************************************************************
ok: [clickhouse-01]

TASK [Start clickhouse service] ********************************************************************************
ok: [clickhouse-01]

TASK [Create database] *****************************************************************************************
ok: [clickhouse-01]

PLAY [Install Vector] ******************************************************************************************

TASK [Gathering Facts] *****************************************************************************************
ok: [vector-01]

TASK [Get vector distrib] **************************************************************************************
ok: [vector-01]

TASK [Install vector packages] *********************************************************************************
ok: [vector-01]

PLAY [Install nginx] *******************************************************************************************

TASK [Gathering Facts] *****************************************************************************************
ok: [lighthouse-01]

TASK [Install nginx] *******************************************************************************************
ok: [lighthouse-01]

PLAY [Install lighthouse] **************************************************************************************

TASK [Gathering Facts] *****************************************************************************************
ok: [lighthouse-01]

TASK [install unzip] *******************************************************************************************
ok: [lighthouse-01]

TASK [Get lighthouse distrib] **********************************************************************************
changed: [lighthouse-01]

TASK [Unarchive lighthouse distrib into nginx] *****************************************************************
ok: [lighthouse-01]

TASK [Make nginx config] ***************************************************************************************
ok: [lighthouse-01]

TASK [Remove lighthouse distrib] *******************************************************************************
--- before
+++ after
@@ -1,4 +1,4 @@
 {
     "path": "./lighthouse.zip",
-    "state": "file"
+    "state": "absent"
 }

changed: [lighthouse-01]
</details>

```bash
PLAY RECAP *****************************************************************************************************
clickhouse-01              : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
lighthouse-01              : ok=8    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector-01                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

9. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-03-yandex` на фиксирующий коммит, в ответ предоставьте ссылку на него.

[Ссылка на Playbook](./playbook)
---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
