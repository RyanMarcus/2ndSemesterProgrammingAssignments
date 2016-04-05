CS12b_PA9.tgz: CS12b_PA9 CS12b_PA9/PA9.html CS12b_PA9/PA9.pdf CS12b_PA9/PA9_Eclipse.zip
	tar czf CS12b_PA9.tgz CS12b_PA9

CS12b_PA9:
	mkdir CS12b_PA9

CS12b_PA9/%.html: %.md
	pandoc -f markdown+simple_tables -t html5 -s -H github.css $< > $@

CS12b_PA9/%.pdf: %.md
	pandoc -f markdown+simple_tables -t latex $< -o $@

CS12b_PA9/PA9_Eclipse.zip: PA9_Eclipse.zip
	cp PA9_Eclipse.zip CS12b_PA9/

.phony: all clean

clean:
	rm -rf CS12b_PA9
	rm -f CS12b_PA9.tgz
