---
layout: post
title: 前端框架学习笔记.
---
# 前端框架学习笔记

这段时间一直在寻找好的前端框架，结合自动化工具简化工作
#### 常用前端JS库
  - [Zepto.js](https://github.com/madrobby/zepto)
  - [jQuery.js](https://github.com/jquery/jquery)
  - [Angular.js](https://github.com/angular/angular.js)
  - [mustache.js](https://github.com/janl/mustache.js)
  - [knockout.js](https://github.com/knockout/knockout)
  
> 轻量级移动端库
  - [Quo.js](https://github.com/soyjavi/QuoJS)
  - [cash.js](https://github.com/kenwheeler/cash)
  - [espresso.js](https://github.com/techlayer/espresso.js)
  
### CSS 库
- [basscss](https://github.com/jxnblk/basscss)
- [semantic-UI](https://github.com/Semantic-Org/Semantic-UI)
- [material-ui](https://github.com/callemall/material-ui)

### 工具库
> alert
- [sweatalert](https://github.com/t4t5/sweetalert)
- [alertify](https://github.com/fabien-d/alertify.js)

> date picker
- [pickadate.js](https://github.com/amsul/pickadate.js)

>image-slider
- [ideal-image-slider](https://github.com/gilbitron/Ideal-Image-Slider)
- [slick](https://github.com/kenwheeler/slick)
- [intense-images](https://github.com/tholman/intense-images)
- [baguettebox](https://github.com/feimosi/baguetteBox.js)

### [Grunt](http://gruntjs.com) 工具
- [grunt-uncss](https://github.com/addyosmani/grunt-uncss)
- [grunt-targethtml](https://github.com/changer/grunt-targethtml)
- [grunt-processhtml](https://github.com/dciccale/grunt-processhtml)
- [grunt-contrib-uglify](https://github.com/gruntjs/grunt-contrib-uglify)
- [grunt-contrib-cssmin](https://github.com/gruntjs/grunt-contrib-cssmin)
- [grunt-contrib-htmlmin](https://github.com/gruntjs/grunt-contrib-htmlmin)
- [grunt-contrib-copy](https://github.com/gruntjs/grunt-contrib-copy)
- [grunt-contrib-concat](https://github.com/gruntjs/grunt-contrib-concat)
- [grunt-shell](https://github.com/sindresorhus/grunt-shell)
- [grunt-contrib-watch](https://github.com/gruntjs/grunt-contrib-watch)
- [grunt-contrib-less](https://github.com/gruntjs/grunt-contrib-less)
- [grunt-contrib-csslint](https://github.com/gruntjs/grunt-contrib-csslint)
- [grunt-contrib-jshint](https://github.com/gruntjs/grunt-contrib-jshint)
- [load-grunt-tasks](https://github.com/sindresorhus/load-grunt-tasks)

> sample Gruntfile.js
``` javascript
module.exports = function(grunt) {
        
    var static_dir = '../bin/static';
    var templates_dir = '../bin/templates';

    grunt.log.writeln(grunt.template.today("HHyyyyssMMdd"));

    grunt.initConfig({
        static_dir: static_dir,
        templates_dir: templates_dir,
        jshint: {
            options: {
                jshintrc: '.jshintrc' 
            },
            release: {
                src: ['js/modules/**/*.js', 'Gruntfile.js']
            }
        },
        concat: {
            min: {
                files:{
                    // ablumns
                    '<%= static_dir %>/dist/js/ablumns.min.js': [
                        'static/js/lib/zepto.min.js',
                        'static/js/lib/image-slider.min.js', 
                        'static/js/lib/slider-helper.min.js', 
                        'static/js/modules/ablumns.min.js'],
                    // comments
                    '<%= static_dir %>/dist/js/comments.min.js': [
                        'static/js/lib/zepto.min.js', 
                        'static/js/lib/image-slider.min.js', 
                        'static/js/lib/slider-helper.min.js', 
                        'static/js/modules/comments.min.js'],
                    // merchant
                    '<%= static_dir %>/dist/js/merchant.min.js': [
                        'static/js/lib/zepto.min.js',
                        'static/js/lib/image-slider.min.js',
                        'static/js/lib/slider-helper.min.js',
                        'static/js/lib/weixinapi.min.js',
                        'static/js/modules/merchant.min.js'],
                    // addcomment
                    '<%= static_dir %>/dist/js/addcomment.min.js': [
                        'static/js/lib/zepto.min.js',
                        'static/js/lib/compressImg.min.js',
                        'static/js/lib/uploader.min.js',
                        'static/js/modules/addcomment.min.js'],
                    // addmember
                    '<%= static_dir %>/dist/js/addmember.min.js': [
                        'static/js/lib/jquery.min.js', 
                        'static/js/lib/picker.min.js', 
                        'static/js/lib/picker-date.min.js', 
                        'static/js/lib/alert.min.js', 
                        'static/js/modules/addmember.min.js'],
                    // index
                    '<%= static_dir %>/dist/js/index.min.js': [
                        'static/js/lib/zepto.min.js', 
                        'static/js/lib/weixinapi.min.js', 
                        'static/js/modules/index.min.js'],
                    // circle
                    '<%= static_dir %>/dist/js/group.min.js': [
                        'static/js/lib/zepto.min.js', 
                        'static/js/lib/weixinapi.min.js', 
                        'static/js/lib/ideal-image-slider.min.js', 
                        'static/js/modules/group.min.js']

                }
            }
        },
        uglify: {
            release: {
                files: [{
                    expand: true,
                    cwd: 'js',
                    src: ['**/**.js'],
                    dest: 'static/js',
                    ext: '.min.js'
                }]
            }
        },
        targethtml: {
            options: {
                curlyTags: {
                    ver: '<%= grunt.template.today("HHyyyyssMMdd") %>' 
                }   
            },  
            release: {
                expand: true,
                cwd: 'html',
                src: '*.html',
                dest: templates_dir
            },
            dev: {
                expand: true,
                cwd: 'html',
                src: '*.html',
                dest: templates_dir
            }
        },
        less: {
            release: {
                expand: true,
                cwd: 'less/modules',
                src: ['*.less'],
                dest: 'static/css',
                ext: '.css'
            },
            dev: {
                expand: true,
                cwd: 'less/modules',
                src: ['*.less'],
                dest: '<%= static_dir %>/css',
                ext: '.css'
            }
        },
        csslint: {
            options: {
                csslintrc: '.csslintrc'
            },
            release: {
                options: {
                    import: 2
                },
                src: ['<%= static_dir %>/css/**/*.css']
            },
            dev: {
                options: {
                    import: 2
                },
                src: ['static/css/**/*.css']
            }
        },
        cssmin: {
            min: {
                files: [{
                    expand: true,
                    cwd: 'static/css',
                    src: '*.css',
                    dest: static_dir + '/dist/css',
                    ext: '.min.css'
                }]
            } 
        },
        clean: {
            options:{
                force: true
            },
            release: {
                src: ['static', '../tmp',  '../bin/static/js', '../bin/static/css']
            }
        },
        copy:{
            js:{
                cwd: 'js',
                expand: true,
                src: ['**/**.js'],
                dest: static_dir + '/js',
                filter: 'isFile'
            }
        },
        connect: {
            server: {
                options: {
                    port: '8077',
                    hostname: '*',
                    livereload: 35729,
                    open: false,
                    base: '../bin'
                }
            }
        },
        watch: {
            options: {
                livereload: true
            },
            dev: {
                files: [ 
                    'html/**/*.html',
                    'less/**/*.less',
                    'js/**/*.js'
                ],
                tasks: ['jshint', 'less:dev', 'copy', 'targethtml:dev']
            }
        },
        shell: {
            options: {
                stderr: true
            },
            target: {
                command: 'cd ../bin; ./debug.sh 8999'
            }
        },
        concurrent: {
            debug: ['shell','dev']
        }
    });
    require('load-grunt-tasks')(grunt);

    grunt.registerTask('default', ['concurrent:debug']);
    grunt.registerTask('dev', ['jshint', 'copy', 'less:dev', 'csslint:dev', 'targethtml:dev', 'watch:dev']);
    grunt.registerTask('release', ['jshint', 'uglify', 'concat', 'less:release', 'csslint:release', 'cssmin',  'targethtml:release', 'clean']);
