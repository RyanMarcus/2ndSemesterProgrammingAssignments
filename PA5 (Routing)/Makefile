CS12b_PA5.tgz: CS12b_PA5 CS12b_PA5/PA5.html CS12b_PA5/PA5.pdf CS12b_PA5/PA5_Eclipse.zip
	tar czf CS12b_PA5.tgz CS12b_PA5

CS12b_PA5:
	mkdir CS12b_PA5

CS12b_PA5/%.html: %.md
	pandoc -f markdown+simple_tables -t html5 -s -H github.css $< > $@

CS12b_PA5/%.pdf: %.md
	pandoc -f markdown+simple_tables -t latex $< -o $@

CS12b_PA5/PA5_Eclipse.zip: PA5_Eclipse.zip
	cp PA5_Eclipse.zip CS12b_PA5/

.phony: all clean

clean:
	rm -rf CS12b_PA5
	rm -f CS12b_PA5.tgz
