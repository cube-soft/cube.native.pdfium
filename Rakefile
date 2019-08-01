# --------------------------------------------------------------------------- #
#
# Copyright (c) 2010 CubeSoft, Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#  http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
# --------------------------------------------------------------------------- #
require 'rake'
require 'rake/clean'

# --------------------------------------------------------------------------- #
# configuration
# --------------------------------------------------------------------------- #
PROJECT     = "pdfium"
CONFIG      = "release"
PLATFORMS   = ["x86", "x64"]
KINDS       = ["full", "lite"]

# --------------------------------------------------------------------------- #
# clean
# --------------------------------------------------------------------------- #
CLEAN.include("out")

# --------------------------------------------------------------------------- #
# default
# --------------------------------------------------------------------------- #
desc "Clean and build projects."
task :default => [:clean, :build_all]

# --------------------------------------------------------------------------- #
# build
# --------------------------------------------------------------------------- #
desc "Build projects with the specified platform and kind."
task :build, [:platform, :kind] do |_, e|
    e.with_defaults(:platform => PLATFORMS[0])
    e.with_defaults(:kind => KINDS[0])

    execute do
        src  = "args-#{kind}-#{platform}.gn"
        dest = "out/#{CONFIG}-#{kind}-#{platform}"
        RakeFileUtils::mkdir_p(dest)
        RakeFileUtils::cp(src, dest)

        sh("gn gen #{dest}")
        sh("ninja -C #{dest} #{PROJECT}")
    end
end

# --------------------------------------------------------------------------- #
# build_all
# --------------------------------------------------------------------------- #
desc "Build projects with pre-defined platforms and kinds."
task :build_all do
    BRANCHES.product(PLATFORMS).each { |set|
        checkout(set[0]) do
            Rake::Task[:build].reenable
            Rake::Task[:build].invoke(set[1])
        end
    }
end

# --------------------------------------------------------------------------- #
# execute
# --------------------------------------------------------------------------- #
def execute(branch, &callback)
    RakeFileUtils::cp("BUILD.gn", "#{PROJECT}/BUILD.gn")
    cd(PROJECT) do
        begin
            callback.call()
        ensure
            sh("git checkout .")
        end
    end
end
