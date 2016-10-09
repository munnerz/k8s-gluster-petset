ARCH ?= $(shell go env GOARCH)
GOOS ?= $(shell go env GOOS)
REPO ?= "github.com/heketi/heketi"
VERSION ?= "v2.0.6"

.DEFAULT: server

git:
	-go get -u -d "${REPO}"
	-cd "${GOPATH}"/src/"${REPO}"; git checkout "${VERSION}"
	-ln -s Godeps/_workspace/src/ "${GOPATH}"/src/"${REPO}"/vendor

gox: git
	go get github.com/mitchellh/gox

server: gox
	CGO_ENABLED=0 "${GOPATH}"/bin/gox -arch="${ARCH}" -os="${GOOS}" -ldflags="-a -installsuffix cgo -X main.HEKETI_VERSION=${VERSION}"  "${REPO}"

client: gox
	CGO_ENABLED=0 "${GOPATH}"/bin/gox -arch="${ARCH}" -os="${GOOS}" -ldflags="-a -installsuffix cgo -X main.HEKETI_VERSION=${VERSION}"  "${REPO}"/client/cli/go/

all: server client