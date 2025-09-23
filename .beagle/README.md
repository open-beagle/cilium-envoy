# cilium-envoy

<https://github.com/cilium/proxy>

## branch

```bash
git remote add upstream git@github.com:cilium/proxy.git
git fetch upstream
git merge upstream/v1.34
```

## cilium-envoy-builder

```bash
# builder
docker pull quay.io/cilium/cilium-envoy-builder:v1.34 && \
docker tag quay.io/cilium/cilium-envoy-builder:v1.34 registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-amd64 && \
docker push registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-amd64

docker pull --platform=linux/arm64 quay.io/cilium/cilium-envoy-builder:v1.34 && \
docker tag quay.io/cilium/cilium-envoy-builder:v1.34 registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-arm64 && \
docker push registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-arm64

docker run \
  -it \
  --rm \
  -e PLUGIN_PLATFORMS=linux/amd64,linux/arm64 \
  -e PLUGIN_TEMPLATE=registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-ARCH \
  -e PLUGIN_TARGET=registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34 \
  -e PLUGIN_USERNAME=$PLUGIN_REGISTRY_USER \
  -e PLUGIN_PASSWORD=$PLUGIN_REGISTRY_PASSWORD \
  registry.cn-qingdao.aliyuncs.com/wod/devops-docker-manifest:1.0

# archive
docker pull quay.io/cilium/cilium-envoy-builder:v1.34-archive-latest && \
docker tag quay.io/cilium/cilium-envoy-builder:v1.34-archive-latest registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-archive-amd64 && \
docker push registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-archive-amd64

docker pull --platform=linux/arm64 quay.io/cilium/cilium-envoy-builder:v1.34-archive-latest && \
docker tag quay.io/cilium/cilium-envoy-builder:v1.34-archive-latest registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-archive-arm64 && \
docker push registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-archive-arm64

docker run \
  -it \
  --rm \
  -e PLUGIN_PLATFORMS=linux/amd64,linux/arm64 \
  -e PLUGIN_TEMPLATE=registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-archive-ARCH \
  -e PLUGIN_TARGET=registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-archive \
  -e PLUGIN_USERNAME=$PLUGIN_REGISTRY_USER \
  -e PLUGIN_PASSWORD=$PLUGIN_REGISTRY_PASSWORD \
  registry.cn-qingdao.aliyuncs.com/wod/devops-docker-manifest:1.0
```

## build

```bash
# multi arch (amd64 , arm64)
ARCH=multi make docker-image-envoy

# arm64
ARCH=arm64 make docker-image-envoy

# buildx arm64
git rev-parse HEAD > SOURCE_VERSION && \
DOCKER_BUILDKIT=1 \
docker buildx build \
  --output type=docker \
  --platform=linux/arm64  \
  --build-arg BUILDER_BASE="registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-arm64" \
  --build-arg ARCHIVE_IMAGE="registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:builder-v1.34-archive-arm64" \
  --build-arg BASE_IMAGE="registry.cn-qingdao.aliyuncs.com/wod/ubuntu:24.04-arm64" \
  --build-arg BAZEL_BUILD_OPTS="--define tcmalloc=disabled --remote_upload_local_results=false" \
  -t registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:1.34.7-arm64 \
  -t registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:1.34-arm64 \
  -f .beagle/Dockerfile \
  . 

docker push registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:1.34.7-arm64 && \
docker push registry.cn-qingdao.aliyuncs.com/wod/cilium-envoy:1.34-arm64
```
