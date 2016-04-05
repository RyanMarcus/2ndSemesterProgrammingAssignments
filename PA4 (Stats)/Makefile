CS12b_PA4.tgz: CS12b_PA4 CS12b_PA4/PA4.html CS12b_PA4/PA4.pdf CS12b_PA4/PA4_Eclipse.zip
	tar czf CS12b_PA4.tgz CS12b_PA4

CS12b_PA4:
	mkdir CS12b_PA4

CS12b_PA4/%.html: %.md
	pandoc -f markdown+simple_tables -t html5 -s -H github.css $< > $@

CS12b_PA4/%.pdf: %.md
	pandoc -f markdown+simple_tables -t latex $< -o $@

CS12b_PA4/PA4_Eclipse.zip: PA4_Eclipse.zip
	cp PA4_Eclipse.zip CS12b_PA4/

.phony: all clean

clean:
	rm -rf CS12b_PA4
	rm -f CS12b_PA4.tgz
