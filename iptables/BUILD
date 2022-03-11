package(default_visibility = ["//visibility:public"])

load("@package_bundle_amd64_debian11//file:packages.bzl", packages_amd64_debian11 = "packages")
load("@io_bazel_rules_docker//container:container.bzl", "container_image", "container_bundle")
load("//:checksums.bzl", "ARCHITECTURES")

ARCHITECTURES = ["amd64"]

DISTRO_SUFFIXES = ["_debian11"]

DISTRO_PACKAGES = {
    "amd64": {
        "_debian11": packages_amd64_debian11,
    },
}

DISTRO_REPOSITORY = {
    "amd64": {
        "_debian11": "@amd64_debian11",
    },
}

# An image for iptables
[
    container_image(
        name = "iptables" + "_" + arch,
        architecture = arch,
        base = "//base:static" + "_" + "nonroot" + "_" + arch + "_debian11",
        debs = [
            # libc
            DISTRO_PACKAGES[arch]["_debian11"]["libc6"],

            # iptables
            DISTRO_PACKAGES[arch]["_debian11"]["iptables"],
            DISTRO_PACKAGES[arch]["_debian11"]["libip4tc2"],
            DISTRO_PACKAGES[arch]["_debian11"]["libip6tc2"],
            DISTRO_PACKAGES[arch]["_debian11"]["libxtables12"],
            DISTRO_PACKAGES[arch]["_debian11"]["libnfnetlink0"],
            DISTRO_PACKAGES[arch]["_debian11"]["libnetfilter-conntrack3"],
            DISTRO_PACKAGES[arch]["_debian11"]["libmnl0"],

            ## conntrack
            DISTRO_PACKAGES[arch]["_debian11"]["conntrack"],
        ],
        symlinks = {
          "/usr/sbin/iptables": "/usr/sbin/iptables-legacy",
          "/usr/sbin/iptables-restore": "/usr/sbin/iptables-legacy-restore",
          "/usr/sbin/iptables-save": "/usr/sbin/iptables-legacy-save",
          "/usr/sbin/ip6tables": "/usr/sbin/ip6tables-legacy",
          "/usr/sbin/ip6tables-restore": "/usr/sbin/ip6tables-legacy-restore",
          "/usr/sbin/ip6tables-save": "/usr/sbin/ip6tables-legacy-save",
        },
    )
    for arch in ARCHITECTURES
]

container_bundle(
  name = "iptables",
  images = {
    "$(HUB)/iptables:latest-" + arch: "//iptables:iptables_" + arch
    for arch in ARCHITECTURES
  }
)
load("@io_bazel_rules_docker//contrib:push-all.bzl", "container_push")

container_push(
    name = "publish",
    bundle = ":iptables",
    format = "Docker",
    sequential = True,
)