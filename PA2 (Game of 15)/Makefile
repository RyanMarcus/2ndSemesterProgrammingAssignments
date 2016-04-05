CS12b_PA2.tgz: CS12b_PA2 CS12b_PA2/PA2.html CS12b_PA2/PA2.pdf CS12b_PA2/PA2_Eclipse.zip
	tar czf CS12b_PA2.tgz CS12b_PA2

CS12b_PA2:
	mkdir CS12b_PA2

CS12b_PA2/%.html: %.md
	pandoc -f markdown+simple_tables -t html5 -s -H github.css $< > $@

CS12b_PA2/%.pdf: %.md
	pandoc -f markdown+simple_tables -t latex $< -o $@

CS12b_PA2/PA2_Eclipse.zip:
	cp PA2_Eclipse.zip CS12b_PA2/

.phony: all clean

clean:
	rm -rf CS12b_PA2
	rm -f CS12b_PA2.tgz
