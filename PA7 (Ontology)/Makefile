CS12b_PA7.tgz: CS12b_PA7 CS12b_PA7/PA7.html CS12b_PA7/PA7.pdf CS12b_PA7/PA7_Eclipse.zip
	tar czf CS12b_PA7.tgz CS12b_PA7

CS12b_PA7:
	mkdir CS12b_PA7

CS12b_PA7/%.html: %.md
	pandoc -f markdown+simple_tables -t html5 -s -H github.css $< > $@

CS12b_PA7/%.pdf: %.md
	pandoc -f markdown+simple_tables -t latex $< -o $@

CS12b_PA7/PA7_Eclipse.zip: PA7_Eclipse.zip
	cp PA7_Eclipse.zip CS12b_PA7/

.phony: all clean

clean:
	rm -rf CS12b_PA7
	rm -f CS12b_PA7.tgz
