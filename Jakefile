/*
 *  Copyright 2011 Research In Motion Limited.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 * http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

var utils = require('./scripts/utils'),
	jWorkflow = require('jWorkflow'),
    childProcess = require('child_process'),
    DESC_NEW_LINE = "\n\t\t\t   # ";

desc("runs clean, hint, build and test");
task('default', ['clean', 'hint', 'build', 'test'], function () {});

desc("clean");
task('clean', [], function () {
    var cmd = 'make clean',
        cleanTask = jWorkflow.order();

    cleanTask.andThen(utils.execCommandWithJWorkflow(cmd, {}, true));
    cleanTask.start(function (error) {
        if (error) {
            utils.displayOutput("Clean FAILED");
            process.exit(1);
        } else {
            utils.displayOutput("Clean SUCCESS");
        }
        complete();
    });
}, true);

desc("hint");
task('hint', [], function () {
    require('./scripts/lint')(Array.prototype.slice.call(arguments), complete);
}, true);

desc("build");
task('build', [], function () {
    var build = jWorkflow.order(function (prev, baton) {
            baton.take();
            require('./scripts/build')(function () {
                baton.pass();
            });
        }).start(function () {
            complete();
        });
}, true);

desc("run unit tests in node - jake test [pluginId]");
task('test', [], function () {
    require('./scripts/test')(null, process.argv.length >= 4 ? process.argv[3] : null);
}, true);

desc("deploy and run test app on device/simulator" + DESC_NEW_LINE +
    "Usage: jake deploy-test-app['branch','targetname','ip','targettype','password']" + DESC_NEW_LINE +
    "Example: jake deploy-test-app[nextBB10,mybb10,169.254.0.1,device,password]");
task('deploy-test-app', ['build'], require('./scripts/test-app'), true);

desc("deploy and run test suite on device/simulator" + DESC_NEW_LINE +
    "Usage: jake deploy-test-suite['branch','targetname','ip','targettype','password']" + DESC_NEW_LINE +
    "Example: jake deploy-test-suite[nextBB10,mybb10,169.254.0.1,device,password]");
task('deploy-test-suite', [], require('./scripts/test-suite'));

desc("deploy and run cordova-mobile-spec on device/simulator" + DESC_NEW_LINE +
    "Usage: jake deploy-mobile-spec['cordova-blackberry-branch','targetname','ip','targettype','password','mobile-spec-branch']" + DESC_NEW_LINE +
    "Example: jake deploy-mobile-spec[master,z10,169.254.0.1,device,abc123,master]");
task('deploy-mobile-spec', [], require('./scripts/mobile-spec'));

desc("run mobile spec auto test on device" + DESC_NEW_LINE +
    "Usage: jake autorun-mobile-spec['cordova-blackberry-branch','targetname','ip','targettype','password','mobile-spec-branch','couchdbIP']" + DESC_NEW_LINE +
    "Example: jake autorun-mobile-spec[blackberry10,z10,169.254.0.1,device,abc123,master,169.254.0.2]");
task('autorun-mobile-spec', [], require('./scripts/autorun-mobile-spec'));
