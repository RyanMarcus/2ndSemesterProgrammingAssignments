CS12b_PA10.tgz: CS12b_PA10 CS12b_PA10/PA10.html CS12b_PA10/PA10.pdf CS12b_PA10/PA10_Eclipse.zip
	tar czf CS12b_PA10.tgz CS12b_PA10

CS12b_PA10:
	mkdir CS12b_PA10

CS12b_PA10/%.html: %.md
	pandoc -f markdown+simple_tables -t html5 -s -H github.css $< > $@

CS12b_PA10/%.pdf: %.md
	pandoc -f markdown+simple_tables -t latex $< -o $@

CS12b_PA10/PA10_Eclipse.zip: PA10_Eclipse.zip
	cp PA10_Eclipse.zip CS12b_PA10/

.phony: all clean

clean:
	rm -rf CS12b_PA10
	rm -f CS12b_PA10.tgz
