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
PATCH       = "../#{PROJECT}.patch"
CONFIG      = "release"
PLATFORMS   = ["x64", "x86"]
KINDS       = ["lite", "full"]

# --------------------------------------------------------------------------- #
# clean
# --------------------------------------------------------------------------- #
CLEAN.include("#{PROJECT}/out")

# --------------------------------------------------------------------------- #
# default
# --------------------------------------------------------------------------- #
desc "Clean, sync, and build projects."
task :default => [:clean, :sync, :build_all]

# --------------------------------------------------------------------------- #
# sync
# --------------------------------------------------------------------------- #
desc "Run glicent sync."
task :sync do
    sh("git submodule update")
    sh("gclient sync --with_branch_heads")
end

# --------------------------------------------------------------------------- #
# build
# --------------------------------------------------------------------------- #
desc "Build projects with the specified kind and platform."
task :build, [:kind, :platform] do |_, e|
    e.with_defaults(:kind => KINDS[0])
    e.with_defaults(:platform => PLATFORMS[0])

    src  = "args/#{e.kind}-#{e.platform}.gn"
    dest = "out/#{CONFIG}-#{e.kind}-#{e.platform}"

    RakeFileUtils::mkdir_p("#{PROJECT}/#{dest}")
    RakeFileUtils::cp(src, "#{PROJECT}/#{dest}/args.gn")

    cd(PROJECT) { build_core(dest) }
end

# --------------------------------------------------------------------------- #
# build_all
# --------------------------------------------------------------------------- #
desc "Build projects with pre-defined kinds and platforms."
task :build_all do
    KINDS.product(PLATFORMS).each { |set|
        Rake::Task[:build].reenable
        Rake::Task[:build].invoke(set[0], set[1])
    }
end

# --------------------------------------------------------------------------- #
# build_core
# --------------------------------------------------------------------------- #
def build_core(dest)
    sh("patch -p1 < #{PATCH}")
    sh("gn gen #{dest}")
    sh("ninja -C #{dest} #{PROJECT}")
ensure
    sh("git checkout .")
    sh("git clean -fd")
end