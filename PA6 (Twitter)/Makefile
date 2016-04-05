CS12b_PA6.tgz: CS12b_PA6 CS12b_PA6/PA6.html CS12b_PA6/PA6.pdf CS12b_PA6/PA6_Eclipse.zip
	tar czf CS12b_PA6.tgz CS12b_PA6

CS12b_PA6:
	mkdir CS12b_PA6

CS12b_PA6/%.html: %.md
	pandoc -f markdown+simple_tables -t html5 -s -H github.css $< > $@

CS12b_PA6/%.pdf: %.md
	pandoc -f markdown+simple_tables -t latex $< -o $@

CS12b_PA6/PA6_Eclipse.zip: PA6_Eclipse.zip
	cp PA6_Eclipse.zip CS12b_PA6/

.phony: all clean

clean:
	rm -rf CS12b_PA6
	rm -f CS12b_PA6.tgz
