thisdir = class/System.Web/Test/tools

include ../../../../build/rules.make

CLASSLIB_DIR = $(topdir)/class/lib/$(PROFILE)
STANDALONE_RUNNER_SUPPORT_ASSEMBLY = $(CLASSLIB_DIR)/standalone-runner-support.dll

STANDALONE_RUNNER_SOURCES = \
	standalone-runner.cs \
	../../../Mono.Options/Mono.Options/Options.cs \

STANDALONE_RUNNER_REFERENCES = \
	-r:$(STANDALONE_RUNNER_SUPPORT_ASSEMBLY) \
	-r:$(CLASSLIB_DIR)/System.Web.dll \
	-r:$(CLASSLIB_DIR)/System.dll

CACHE_PQ_TEST_GENERATOR_SOURCES = \
	CachePQTestGenerator/CacheItemComparer.cs \
	CachePQTestGenerator/PriorityQueue.cs \
	CachePQTestGenerator/PriorityQueueState.cs \
	CachePQTestGenerator/Sequences.cs \
	CachePQTestGenerator/Utils.cs \
	CachePQTestGenerator/cache-pq-test-generator.cs \
	../../System.Web.Caching/CacheItem.cs \
	../../System.Web.Caching/CacheItemPriorityQueue.cs \
	../../System.Web.Caching/CacheItemPriorityQueueDebug.cs \
	../System.Web.Caching/CacheItemPriorityQueueTestSupport.cs

CACHE_PQ_TEST_GENERATOR_REFERENCES = \
	-pkg:dotnet

CACHE_PQ_TEST_SEQUENCES = $(wildcard ./CachePQTestGenerator/Sequences/*.seq)
CACHE_PQ_TEST_PACKED_SEQUENCES = $(wildcard ./CachePQTestGenerator/Sequences/*.seq.gz)

all-local: HtmlWriter.dll standalone-runner.exe cache-pq-test-generator.exe

HtmlWriter.dll: HtmlWriter.cs
	$(CSCOMPILE) -t:library -r:System.Web.dll $<

standalone-runner.exe: deps $(STANDALONE_RUNNER_SOURCES)
	$(CSCOMPILE) $(STANDALONE_RUNNER_REFERENCES) -out:$@ $(STANDALONE_RUNNER_SOURCES)

cache-pq-test-generator.exe: $(CACHE_PQ_TEST_GENERATOR_SOURCES)
	$(CSCOMPILE) -d:DEBUG $(CACHE_PQ_TEST_GENERATOR_REFERENCES) -out:$@ $(CACHE_PQ_TEST_GENERATOR_SOURCES)

generate-cache-pq-tests: cache-pq-test-generator.exe 
	for f in $(patsubst %.seq.gz,%.seq,$(CACHE_PQ_TEST_PACKED_SEQUENCES)); do \
		gunzip -c $$f.gz > $$f ; \
	done ;
	$(RUNTIME) --debug cache-pq-test-generator.exe ./CachePQTestGenerator/Sequences/ ../System.Web.Caching/CacheItemPriorityQueueTestData/ > ../System.Web.Caching/CacheItemPriorityQueueTest_generated.cs

deps:
ifndef STANDALONE_SUPPORT_BUILT
	$(MAKE) -C ../../ standalone-runner-support
endif

clean:
	rm -f HtmlWriter.dll HtmlWriter.*db
	rm -f standalone-runner.exe standalone-runner.*db
	for f in $(patsubst %.seq.gz,%.seq,$(CACHE_PQ_TEST_PACKED_SEQUENCES)); do \
		rm -f $$f ; \
	done ;
