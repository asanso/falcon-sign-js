.POSIX:

# =====================================================================
#
# Configurable options:
#   CC       C compiler; GCC or Clang are fine; MSVC (2015+) works too.
#   CFLAGS   Compilation flags:
#             * Optimization level -O2 or higher is recommended
#            See config.h for some possible configuration macros.
#   LD       Linker; normally the same command as the compiler.
#   LDFLAGS  Linker options, not counting the extra libs.
#   LIBS     Extra libraries for linking:
#             * If using the native FPU, test_falcon and application
#               code that calls this library may need: -lm
#               (normally not needed on x86, both 32-bit and 64-bit)

CC = gcc
CFLAGS = -Wall -Wextra -Wshadow -Wundef -O3 #-pg -fno-pie
CFLAGS += -DFALCON_PRNG_KECCAK256=1 # uses keccak prng
LD = gcc
LDFLAGS = #-pg -no-pie
LIBS = #-lm

# =====================================================================

# Conditionally set object files based on PRNG choice
BASE_OBJ = api.o codec.o common.o fft.o fpr.o keygen.o rng.o sign.o vrfy.o

# Extract FALCON_PRNG_KECCAK256 value from CFLAGS
PRNG_TYPE = $(shell echo "$(CFLAGS)" | grep -o 'FALCON_PRNG_KECCAK256=[0-1]' | cut -d'=' -f2)

ifeq ($(PRNG_TYPE),0)
    PRNG_OBJ = shake.o
else
    PRNG_OBJ = keccak_prng.o keccak256.o
endif

OBJ = $(BASE_OBJ) $(PRNG_OBJ)

all: test_falcon test_prng

clean:
	-rm -f $(OBJ) test_falcon test_falcon.o test_prng test_prng.o

test_falcon: test_falcon.o $(OBJ)
	$(LD) $(LDFLAGS) -o test_falcon test_falcon.o $(OBJ) $(LIBS)

test_prng: test_prng.o $(PRNG_OBJ)
	$(LD) $(LDFLAGS) -o test_prng test_prng.o $(PRNG_OBJ) $(LIBS)

codec.o: codec.c inner.h fpr.h
	$(CC) $(CFLAGS) -c -o codec.o codec.c

common.o: common.c inner.h fpr.h
	$(CC) $(CFLAGS) -c -o common.o common.c

api.o: api.c api.h inner.h fpr.h
	$(CC) $(CFLAGS) -c -o api.o api.c

fft.o: fft.c inner.h fpr.h
	$(CC) $(CFLAGS) -c -o fft.o fft.c

fpr.o: fpr.c inner.h fpr.h
	$(CC) $(CFLAGS) -c -o fpr.o fpr.c

keygen.o: keygen.c inner.h fpr.h
	$(CC) $(CFLAGS) -c -o keygen.o keygen.c

rng.o: rng.c inner.h fpr.h
	$(CC) $(CFLAGS) -c -o rng.o rng.c

shake.o: shake.c inner.h fpr.h
	$(CC) $(CFLAGS) -c -o shake.o shake.c

sign.o: sign.c inner.h fpr.h
	$(CC) $(CFLAGS) -c -o sign.o sign.c

speed.o: speed.c api.h
	$(CC) $(CFLAGS) -c -o speed.o speed.c

test_falcon.o: test_falcon.c api.h inner.h fpr.h
	$(CC) $(CFLAGS) -c -o test_falcon.o test_falcon.c

vrfy.o: vrfy.c inner.h fpr.h
	$(CC) $(CFLAGS) -c -o vrfy.o vrfy.c

keccak_prng.o: keccak_prng.c keccak256.h
	$(CC) $(CFLAGS) -c -o keccak_prng.o keccak_prng.c

keccak256.o: keccak256.c keccak256.h
	$(CC) $(CFLAGS) -c -o keccak256.o keccak256.c

test_prng.o: test_prng.c keccak256.h
	$(CC) $(CFLAGS) -c -o test_prng.o test_prng.c