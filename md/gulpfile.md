```js
/**
 * series顺序执行，parallel并行执行
 */
const { src, dest, series, parallel, task, watch, lastRun } = require('gulp');
const less = require('gulp-less');
const uglify = require('gulp-uglify');
const babel = require('gulp-babel');
const sourcemaps = require('gulp-sourcemaps');
const rev = require('gulp-rev');//文件名加哈希
const revCollector = require('gulp-rev-collector');//替换html中的文件链接
const del = require('del');
const minimist = require('minimist');//获取命令参数
const gulpif = require('gulp-if');//gulp流程控制
const changed = require('gulp-changed');//增量操作
const imagemin = require('gulp-imagemin');// 压缩图片
const revDistClean = require('gulp-rev-dist-clean');
const { exec, spawn } = require('child_process');
const argv = minimist(process.argv.slice(2));
console.log(argv);

//是否生产，
const prd = (argv.env === 'prd' || argv._[0] === 'build');

// 文件路径
const paths = {
    styles: {
        src: 'src/client/public/stylesheets/**/*.less',
        dest: 'dest/client/public/stylesheets/'
    },
    scripts: {
        src: 'src/client/public/javascripts/**/*.js',
        dest: 'dest/client/public/javascripts/'
    },
    imgs: {
        src: 'src/client/public/images/**/*.@(jpg|png|ico|gif)',
        dest: 'dest/client/public/images/'
    },
    tpl: {
        src: 'src/client/pages/**/*.art',
        dest: 'dest/client/pages/'
    },
    serverjs: {
        src: 'src/server/**/*.js',
        dest: 'dest/server/'
    },
    vender: {
        src: 'src/client/public/lib/**/*',
        dest: 'dest/client/public/lib/'
    }
};

//copy js文件到dest，同时压缩，哈希处理
task('cpjs', () => {
    return src(paths.scripts.src)
        .pipe(babel({
            presets: ['@babel/env'],
            ignore: ['./src/client/public/lib/']
        }))
        .pipe(uglify())
        .pipe(changed(paths.scripts.dest))
        .pipe(gulpif(prd, rev()))//生产环境加哈希
        .pipe(dest(paths.scripts.dest))
        .pipe(rev.manifest())
        .pipe(dest(paths.scripts.dest))// 源文件和哈希文件的映射
});

//copy serverjs文件到dest
task('cpserverjs', () => {
    return src(paths.serverjs.src)
        .pipe(changed(paths.serverjs.dest))
        .pipe(dest(paths.serverjs.dest))
});

task('cptpl', () => {
    return src(paths.tpl.src)
        .pipe(changed(paths.tpl.dest))//增量copy，不修改不copy

        .pipe(dest(paths.tpl.dest))
})

task('cpimg', () => {
    return src(paths.imgs.src)
        .pipe(changed(paths.imgs.dest))
        .pipe(gulpif(prd, rev()))//生产环境加哈希
        .pipe(dest(paths.imgs.dest))
        .pipe(rev.manifest())
        .pipe(dest(paths.imgs.dest))// 源文件和哈希文件的映射
    // .pipe(src(paths.imgs.dest + '**/*', { read: false }))
    // .pipe(revDistClean(paths.imgs.dest + 'rev-manifest.json'))
});

task('cpcss', () => {
    return src(paths.styles.src)
        .pipe(less())
        .pipe(changed(paths.styles.dest))
        .pipe(gulpif(prd, rev()))//生产环境加哈希
        .pipe(dest(paths.styles.dest))
        .pipe(rev.manifest())
        .pipe(dest(paths.styles.dest))// 源文件和哈希文件的映射
    // .pipe(src(paths.styles.dest + '**/*', { read: false }))
    // .pipe(revDistClean(paths.styles.dest + 'rev-manifest.json'))

});

//替换html中的静态文件哈希文件
task('rev', () => {
    return src(['./dest/client/public/**/rev-manifest.json', './dest/client/pages/**/*.art'])
        .pipe(revCollector({
            replaceReved: true
        }))
        .pipe(dest('dest/client/pages'))
})

task('start', () => {
    const server_process = exec(`npx nodemon ./dest/server/bin/www.js`, (err, stdout, stderr) => {
        console.log(err)
    })
    server_process.stdout.pipe(process.stdout)
    server_process.stderr.pipe(process.stderr)
    watch('./src/client/**/*.art', parallel('cptpl'))
    watch('./src/client/**/*.less', parallel('cpcss'))
    watch('./src/client/**/*.js', parallel('cpjs'))
    watch('./src/client/**/*.@(jpg|png|ico|gif)', parallel('cpimg'))
})

// 清理dest目录文件
task('clean', () => {
    return del([
        paths.styles.dest,
        paths.tpl.dest,
        paths.scripts.dest,
        paths.serverjs.dest,
        paths.imgs.dest
    ]);
});

// 删除过期哈希文件，避免累积过多，在哈希模式下执行，开发模式下不会加哈希
task('revClean', () => {
    return src(paths.scripts.dest + '**/*', { read: false })
        .pipe(revDistClean(paths.scripts.dest + 'rev-manifest.json'))// 删除过期文件
        .pipe(paths.styles.dest + '**/*', { read: false })
        .pipe(revDistClean(paths.scripts.dest + 'rev-manifest.json'))// 删除过期文件
        .pipe(paths.imgs.dest + '**/*', { read: false })
        .pipe(revDistClean(paths.imgs.dest + 'rev-manifest.json'))// 删除过期文件
});

//同步第三方静态文件
task('sync-static', () => {
    return src(paths.vender.src, {
        allowEmpty: true
    }).pipe(dest(paths.vender.dest));
});

/**
 * 开发环境
 * 
 */
task('dev', series('clean', parallel('cpjs', 'cpimg', 'cpcss', 'cpserverjs'), 'cptpl', 'start'));

/**
 * 线上环境，清理dest，copy静态文件，哈希处理，只编译，不服务
 */
task('build', series('clean', parallel('cpjs', 'cpimg', 'cpcss', 'cpserverjs', 'sync-static'), 'cptpl', 'rev'));
```
