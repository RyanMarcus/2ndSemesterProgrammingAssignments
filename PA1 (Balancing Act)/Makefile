CS12b_PA1.tgz: CS12b_PA1 CS12b_PA1/PA1.html CS12b_PA1/PA1.pdf CS12b_PA1/PA1_Eclipse.zip
	tar czf CS12b_PA1.tgz CS12b_PA1

CS12b_PA1:
	mkdir CS12b_PA1

CS12b_PA1/%.html: %.md
	pandoc -f markdown+simple_tables -t html5 -s -H github.css $< > $@

CS12b_PA1/%.pdf: %.md
	pandoc -f markdown+simple_tables -t latex $< -o $@

CS12b_PA1/PA1_Eclipse.zip:
	cp PA1_Eclipse.zip CS12b_PA1/

.phony: all clean

clean:
	rm -rf CS12b_PA1
	rm -f CS12b_PA1.tgz
