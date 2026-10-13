.PHONY: test test_clang test_clang_rust_target test_gcc test_asan test_tsan clean

MAKEFLAGS += --no-print-directory
BUILD_DIR := build

test: test_clang test_clang_rust_target test_gcc

test_clang:
	$(MAKE) -C asan test_clang BUILD_DIR="../$(BUILD_DIR)"
	$(MAKE) -C tsan test_clang BUILD_DIR="../$(BUILD_DIR)"

test_clang_rust_target:
	@set -e; if [ -f /etc/redhat-release ]; then \
		native=$$(LC_ALL=C clang++ -v -x c++ -fsyntax-only /dev/null 2>&1) || { printf '%s\n' "$$native"; exit 1; }; \
		rust=$$(LC_ALL=C clang++ --target="$$(uname -m)-unknown-linux-gnu" -v -x c++ -fsyntax-only /dev/null 2>&1) || { printf '%s\n' "$$rust"; exit 1; }; \
		native=$$(printf '%s\n' "$$native" | sed -n 's/^Selected GCC installation: //p'); \
		rust=$$(printf '%s\n' "$$rust" | sed -n 's/^Selected GCC installation: //p'); \
		printf 'Native GCC: %s\nRust target GCC: %s\n' "$$native" "$$rust"; \
		test -n "$$native"; \
		test "$$native" = "$$rust"; \
		printf '#include <ranges>\n' | \
			clang++ --target="$$(uname -m)-unknown-linux-gnu" -std=c++20 -x c++ -fsyntax-only -; \
	fi

test_gcc:
	@if [ -f /etc/os-release ] && grep -qi 'pine' /etc/os-release; then \
		echo "Skipping g++ sanitizer tests on Alpine."; \
	else \
		$(MAKE) -C asan test_gcc BUILD_DIR="../$(BUILD_DIR)"; \
		$(MAKE) -C tsan test_gcc BUILD_DIR="../$(BUILD_DIR)"; \
	fi

test_asan:
	$(MAKE) -C asan test BUILD_DIR="../$(BUILD_DIR)"

test_tsan:
	$(MAKE) -C tsan test BUILD_DIR="../$(BUILD_DIR)"

clean:
	$(MAKE) -C asan clean BUILD_DIR="../$(BUILD_DIR)"
	$(MAKE) -C tsan clean BUILD_DIR="../$(BUILD_DIR)"
