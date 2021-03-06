IMAGE=jenkinsci/pipeline-as-code-github-demo
TAG=$(shell date -I -u)
DOCKER_RUN=docker run --rm -p 8080:8080 -p 4040:4040 -v /var/run/docker.sock:/var/run/docker.sock --group-add=$(shell stat -c %g /var/run/docker.sock) -ti

copy-plugins:
	set -e; \
	rm -rf plugins; \
	mkdir plugins; \
	for gav in `cat plugins.txt`; do \
	  g=`echo $$gav | cut -f1 -d: | perl -pe 's{[.]}{/}g'`; \
	  a=`echo $$gav | cut -f2 -d:`; \
	  v=`echo $$gav | cut -f3 -d:`; \
	  hpi=$$HOME/.m2/repository/$$g/$$a/$$v/$$a-$$v.hpi; \
	  if [ \! -f $$hpi ]; then \
	    mvn -U org.apache.maven.plugins:maven-dependency-plugin:2.5.1:get -Dartifact=$$gav:hpi -Dtransitive=false ||\
	      (locate $$a-$$v.hpi | fgrep .m2/repository/; false); \
	  fi; \
	  cp -v $$hpi plugins/$$a.jpi; \
	done

build: copy-plugins
	docker build -t $(IMAGE):$(TAG) .

run: build
	$(DOCKER_RUN) $(IMAGE):$(TAG)

push:
	docker push $(IMAGE):$(TAG)
	echo "consider also: make push-latest"

push-latest: push
	docker tag $(IMAGE):$(TAG) $(IMAGE):latest
	docker push $(IMAGE):latest
