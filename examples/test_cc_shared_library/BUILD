load("//cc:defs.bzl", "cc_binary", "cc_library", "cc_test")
load("//examples:experimental_cc_shared_library.bzl", "cc_shared_library")

cc_test(
    name = "cc_test",
    srcs = ["main.cc"],
    dynamic_deps = ["foo_so"],
    deps = ["foo"],
)

cc_binary(
    name = "binary",
    srcs = ["main.cc"],
    dynamic_deps = ["foo_so"],
    deps = ["foo"],
)

cc_shared_library(
    name = "foo_so",
    dynamic_deps = ["bar_so"],
    preloaded_deps = ["preloaded_dep"],
    static_deps = [
        "//examples/test_cc_shared_library:qux",
    ],
    user_link_flags = ["-Wl,-rpath,kittens"],
    visibility_file = "foo.lds",
    exports = [
        "foo",
        # Not a problem to export "baz" which is depended on by foo
        "baz",
        # Case1
        # This is fine. bar3 can be exported by "foo_so"
        # even though bar3 is linked statically by bar_so, since bar_so
        # doesn't export bar3 this is fine.
        "bar3",
        # Case2
        # This is fine. foo depends on bar4. Even though bar is
        # linked dynamically, when bar is pruned, we still have a dependency
        # edge to bar4 from foo. Also bar_so doesn't export bar4.
        #
        # Note that even though the target claims to export bar4. Unless the version script is
        # changed, the symbols from bar4 won't be exported.
        "bar4",
    ],
)

cc_library(
    name = "preloaded_dep",
    srcs = ["preloaded_dep.cc"],
    hdrs = ["preloaded_dep.h"],
)

cc_library(
    name = "foo",
    srcs = ["foo.cc"],
    hdrs = ["foo.h"],
    deps = [
        "preloaded_dep",
        "bar",
        "baz",
        # Not exported.
        "qux",
    ],
)

cc_library(
    name = "baz",
    srcs = ["baz.cc"],
    hdrs = ["baz.h"],
)

cc_library(
    name = "qux",
    srcs = ["qux.cc"],
    hdrs = ["qux.h"],
)

cc_shared_library(
    name = "bar_so",
    visibility_file = "bar.lds",
    exports = [
        "bar",
        "bar2",
    ],
)

cc_library(
    name = "bar",
    srcs = ["bar.cc"],
    hdrs = ["bar.h"],
)

cc_library(
    name = "bar2",
    srcs = ["bar2.cc"],
    hdrs = ["bar2.h"],
)

cc_library(
    name = "bar3",
    srcs = ["bar3.cc"],
    hdrs = ["bar3.h"],
)

cc_library(
    name = "bar4",
    srcs = ["bar4.cc"],
    hdrs = ["bar4.h"],
)

sh_test(
    name = "cc_shared_library_integration_test",
    srcs = ["cc_shared_library_integration_test.sh"],
    data = [
        ":bar_so",
        ":binary",
        ":cc_test",
        ":foo_so",
    ],
)
