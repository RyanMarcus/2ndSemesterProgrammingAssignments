CS12b_PA8.tgz: CS12b_PA8 CS12b_PA8/PA8.html CS12b_PA8/PA8.pdf CS12b_PA8/PA8_Eclipse.zip
	tar czf CS12b_PA8.tgz CS12b_PA8

CS12b_PA8:
	mkdir CS12b_PA8

CS12b_PA8/%.html: %.md
	pandoc -f markdown+simple_tables -t html5 -s -H github.css $< > $@

CS12b_PA8/%.pdf: %.md
	pandoc -f markdown+simple_tables -t latex $< -o $@

CS12b_PA8/PA8_Eclipse.zip: PA8_Eclipse.zip
	cp PA8_Eclipse.zip CS12b_PA8/

.phony: all clean

clean:
	rm -rf CS12b_PA8
	rm -f CS12b_PA8.tgz
