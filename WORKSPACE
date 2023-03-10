# Copyright 2022 The XLS Authors
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

workspace(name = "com_google_xls")

# Load and configure a hermetic LLVM based C/C++ toolchain. This is done here
# and not in load_external.bzl because it requires several sequential steps of
# declaring archives and using things in them, which is awkward to do in .bzl
# files because it's not allowed to use `load` inside of a function.
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

# Commit on  2023-02-09, current as of 2023-03-09.
http_archive(
    name = "com_grail_bazel_toolchain",
    # Patch until https://github.com/grailbio/bazel-toolchain/pull/191 is merged.
    patch_args = ["-p1"],
    patches = ["//dependency_support/com_grail_bazel_toolchain:0001-Fix-call-to-int-version.patch"],
    sha256 = "fbed0cf24304b57682ca63763528f5fa21c341c2eeff3dc53887eb73638059c4",
    strip_prefix = "bazel-toolchain-069ee4e20ec605a6c76c1798658e17175b2eb35e",
    urls = [
        "https://github.com/grailbio/bazel-toolchain/archive/069ee4e20ec605a6c76c1798658e17175b2eb35e.zip",
    ],
)

load("@com_grail_bazel_toolchain//toolchain:deps.bzl", "bazel_toolchain_dependencies")

bazel_toolchain_dependencies()

load("@com_grail_bazel_toolchain//toolchain:rules.bzl", "llvm_toolchain")

llvm_toolchain(
    name = "llvm_toolchain",
    llvm_version = "15.0.6",
)

load("@llvm_toolchain//:toolchains.bzl", "llvm_register_toolchains")

llvm_register_toolchains()

load("//dependency_support:load_external.bzl", "load_external_repositories")

load_external_repositories()

# We have to configure Python before gRPC tries to configure it a different
# way.
load("@pybind11_bazel//:python_configure.bzl", "python_configure")
python_configure(name = "local_config_python", python_version="3")

# gRPC deps should be loaded before initializing other repos. Otherwise, various
# errors occur during repo loading and initialization.
load("@com_github_grpc_grpc//bazel:grpc_deps.bzl", "grpc_deps")

grpc_deps()

load("//dependency_support:initialize_external.bzl", "initialize_external_repositories")

initialize_external_repositories()

# Loading the extra deps must be called after initialize_eternal_repositories or
# the call to pip_install fails.
load("@com_github_grpc_grpc//bazel:grpc_extra_deps.bzl", "grpc_extra_deps")

grpc_extra_deps()
