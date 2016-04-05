CS12b_PA3.tgz: CS12b_PA3 CS12b_PA3/PA3.html CS12b_PA3/PA3.pdf CS12b_PA3/PA3_Eclipse.zip
	tar czf CS12b_PA3.tgz CS12b_PA3

CS12b_PA3:
	mkdir CS12b_PA3

CS12b_PA3/%.html: %.md
	pandoc -f markdown+simple_tables -t html5 -s -H github.css $< > $@

CS12b_PA3/%.pdf: %.md
	pandoc -f markdown+simple_tables -t latex $< -o $@

CS12b_PA3/PA3_Eclipse.zip:
	cp PA3_Eclipse.zip CS12b_PA3/

.phony: all clean

clean:
	rm -rf CS12b_PA3
	rm -f CS12b_PA3.tgz
