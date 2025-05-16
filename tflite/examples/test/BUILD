load("//tflite:build_def.bzl", "tflite_copts", "tflite_copts_warnings", "tflite_linkopts")
load("//tflite:special_rules.bzl", "tflite_portable_test_suite")

package(
    # copybara:uncomment default_applicable_licenses = ["//tensorflow:license"],
    default_visibility = [
        "//visibility:public",
    ],
    licenses = ["notice"],
)

common_copts = tflite_copts() + tflite_copts_warnings()

# We create a library for benchmark_main.cc to faciliate the creation of a
# customized benchmark model binary that only needs linking with extra
# dependency, e.g., enabling creating of benchmark binaries with a custom
# delegate provider.
cc_library(
    name = "test_main",
    srcs = [
        "test_main.cc",
    ],
    copts = common_copts,
    deps = [
        "//tflite:framework",
        "//tflite:simple_memory_arena_debug_dump",
        "//tflite:string_util",
        "//tflite:op_resolver",
        "//tflite/core:cc_api_stable",
        "//tflite/core:framework",
        "//tflite/core/c:c_api_types",
        "//tflite/core/c:common",
        "//tflite/kernels:builtin_ops",
        "//tflite/kernels:cpu_backend_context",
        "//tflite/profiling:model_runtime_info",
        "//tflite/tools:logging",
        "//tflite/tools:model_loader",
        "//tflite/tools:utils",
        "//tflite/nnapi:nnapi_util",
        "//tflite/tools/delegates:delegate_provider_hdr",
        "//tflite/tools/delegates:tflite_execution_providers",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/strings:string_view",
        "@com_google_absl//absl/types:span",
        "@ruy//ruy/profiler",
    ]+ select({
        "@org_tensorflow//tensorflow:android_arm64": [
            "//tflite/delegates/gpu:delegate",
        ],
        "//conditions:default": [],
    }),
)

cc_binary(
    name = "test",
    copts = common_copts,
    linkopts = tflite_linkopts() + select({
        "@org_tensorflow//tensorflow:android": [
            "-pie",  # Android 5.0 and later supports only PIE
            "-lm",  # some builtin ops, e.g., tanh, need -lm
            "-Wl,--rpath=/data/local/tmp/",  # Hexagon delegate libraries should be in /data/local/tmp
        ],
        "//conditions:default": [],
    }),
    tags = ["builder_default_android_arm64"],
    deps = [
        ":test_main",
    ],
)


tflite_portable_test_suite()
