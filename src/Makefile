CXX = g++
CXX_FLAGS = -std=c++17 -Wall -Werror -Wextra
COV_FLAGS = -fprofile-arcs -ftest-coverage --coverage -O0
TARGET = 3D_Viewer
SRC = ./3D_Viewer/model
TEST_DIR = ./tests
TEST = ./tests/test
TEST_FLAGS = -lgtest
MEMORY_CHECKER = leaks -atExit --
REPORT_DIR = ./report

ifeq ($(shell uname -s),Linux)
	TEST_FLAGS += -lpthread
	MEMORY_CHECKER = valgrind -s --leak-check=full --show-leak-kinds=all
endif

all: install test

build:
	mkdir build
	cd build && qmake ../$(TARGET)/$(TARGET).pro && make

install: build
	@test -d ~/$(TARGET) || mkdir -p ~/$(TARGET)
	cp ./build/$(TARGET) ~/$(TARGET)

uninstall:
	rm -rf ~/$(TARGET)
	rm -rf ./build
	rm -rf setting.dat

dvi: $(TARGET).pdf
	open $(TARGET).pdf
	doxygen Doxyfile
	open ./html/index.html

$(TARGET).pdf: info/$(TARGET).tex
	texi2pdf -q -c info/$(TARGET).tex -o $(TARGET).pdf

dist:
	tar -czf $(TARGET).tar.gz $(TARGET)

test:
	$(CXX) $(CXX_FLAGS) $(TEST_DIR)/*.cc $(SRC)/parser.cc $(SRC)/figure.cc $(TEST_FLAGS) -o $(TEST)
	$(TEST)

gcov_report: clean
	$(CXX) $(CXX_FLAGS) $(COV_FLAGS) $(TEST_DIR)/*.cc $(SRC)/parser.cc $(SRC)/figure.cc $(TEST_FLAGS) -o $(TEST)
	$(TEST)
	gcovr -f '3D_Viewer/model' . -s --html --html-details -o ./coverage_report.html
	rm -rf *.gcno *.gcda *.o
	open ./coverage_report.html

style_check:
	@find . \( -name '*.h' -o -name '*.cc' \) -print0 | xargs -0 clang-format -n -style=Google

style_fix:
	@find . \( -name '*.h' -o -name '*.cc' \) -print0 | xargs -0 clang-format -i -style=Google

cppcheck:
	cppcheck --std=c++17 --language=c++ --enable=warning \
	$(TARGET)/main.cc $(TARGET)/model/* $(TARGET)/view/* $(TARGET)/controller/*

cpplint:
	cpplint --recursive --filter=-legal/copyright,-build/include_subdir,-build/header_guard \
	$(TARGET)/main.cc $(TARGET)/model/* $(TARGET)/view/* $(TARGET)/controller/*

leaks_check: test
	CK_FORK=no $(MEMORY_CHECKER) $(TEST)

clean:
	rm -rf build*
	rm -rf report/*
	rm -rf *.pdf
	rm -rf *.gz
	rm -rf tests/test
	rm -rf html latex
	rm -rf coverage*
