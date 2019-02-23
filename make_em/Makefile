# makefile for arm-linux
#   gcc version 4

CC		=	emcc
CXX		=   em++
RM		=	rm -f

ifeq ($(EMSCRIPTEN_ROOT),)
#Set it yourself if EMSCRIPTEN_ROOT not there
	EMSCRIPTEN_ROOT=c:/emsdk/emscripten/1.38.12
endif

#WebAssembly seemed to be a bit faster than asm.js
WASM=1

#Set maximum RAM that Emscripten use (in bytes).
EMSCRIPTEN_TOTAL_MEMORY=83886080

SDL_CFLAGS := -s USE_SDL=1
SDL_LIBS := 

DBX_PATH	= ..

CFLAGS	=	-O3 -DNDEBUG -DEMTERPRETER_SYNC -Wstrict-overflow=0 -Wstrict-aliasing=0 -I. \
			-I$(DBX_PATH) \
			-I$(DBX_PATH)/include \
			-I$(DBX_PATH)/src 
			
CFLAGS	+=	$(SDL_CFLAGS)

CXXFLAGS = -O3 -DNDEBUG -DEMTERPRETER_SYNC -std=gnu++98  -Wstrict-overflow=0 -Wstrict-aliasing=0 -I. \
			-I$(DBX_PATH) \
			-I$(DBX_PATH)/include \
			-I$(DBX_PATH)/src 
			
CXXFLAGS	+=	$(SDL_CFLAGS)
	
LFLAGS	=

TARGET	=	dosbox.bc

CPPSRCS	=	$(DBX_PATH)/src/dosbox.cpp \
			$(wildcard $(DBX_PATH)/src/aviwriter/*.cpp) \
			$(wildcard $(DBX_PATH)/src/builtin/*.cpp) \
			$(wildcard $(DBX_PATH)/src/cpu/*.cpp) \
			$(wildcard $(DBX_PATH)/src/debug/*.cpp) \
			$(wildcard $(DBX_PATH)/src/dos/*.cpp) \
			$(wildcard $(DBX_PATH)/src/fpu/*.cpp) \
			$(wildcard $(DBX_PATH)/src/gui/*.cpp) \
			$(wildcard $(DBX_PATH)/src/gui/*.c) \
			$(wildcard $(DBX_PATH)/src/hardware/*.cpp) \
			$(wildcard $(DBX_PATH)/src/hardware/mame/*.cpp) \
			$(wildcard $(DBX_PATH)/src/hardware/serialport/*.cpp) \
			$(wildcard $(DBX_PATH)/src/ints/*.cpp) \
			$(wildcard $(DBX_PATH)/src/libs/gui_tk/*.cpp) \
			$(wildcard $(DBX_PATH)/src/misc/*.cpp) \
			$(wildcard $(DBX_PATH)/src/shell/*.cpp) 

CPPSRCS_MT32 = $(wildcard $(DBX_PATH)/src/mt32/*.cpp) \
			$(wildcard $(DBX_PATH)/src/mt32/sha1/*.cpp)

OBJS_MT32 = $(addsuffix .o,$(basename $(CPPSRCS_MT32)))

CXXFLAGS_MT32 = -DC_MT32=1 -I$(DBX_PATH)/src/mt32 -I$(DBX_PATH)/src/mt32/sha1

OBJS = $(addsuffix .o,$(basename $(CPPSRCS)))
LIBS = -lm  $(SDL_LIBS) 

ifeq ($(MT32),1)
	CPPSRCS += $(CPPSRCS_MT32)
	CXXFLAGS += $(CXXFLAGS_MT32)
	OBJS += $(OBJS_MT32)
endif

.SUFFIXES: .c.o
.SUFFIXES: .cpp.o

all:	$(TARGET)

$(TARGET):	$(OBJS)
	$(CC) $(LFLAGS) -g -o $@ $(OBJS) $(LIBS)
	
.c.o:
	$(CC) $(CFLAGS) -c -o $@ $<

.cpp.o:
	$(CXX) $(CXXFLAGS) -c -o $@ $<

clean:
	rm -f $(TARGET) $(OBJS) $(OBJS_MT32) $(basename $(TARGET)).html $(basename $(TARGET)).js.orig.js  $(basename $(TARGET)).js $(basename $(TARGET)).wasm $(basename $(TARGET)).data

html: $(TARGET)
ifeq ($(PRELOAD), 1)
	$(CC) -O3 $(SDL_CFLAGS) -s WASM=$(WASM) -s TOTAL_MEMORY=$(EMSCRIPTEN_TOTAL_MEMORY)  -s EMTERPRETIFY=1 -s EMTERPRETIFY_ASYNC=1 \
	-s EMTERPRETIFY_WHITELIST=@$(DBX_PATH)/src/em.txt -s BINARYEN_TRAP_MODE="js" -s ERROR_ON_UNDEFINED_SYMBOLS=0 $(TARGET) \
	--preload-file $(PREFILE) -o $(basename $(TARGET)).html 
else
	$(CC) -O3 $(SDL_CFLAGS) -s WASM=$(WASM) -s TOTAL_MEMORY=$(EMSCRIPTEN_TOTAL_MEMORY)  -s EMTERPRETIFY=1 -s EMTERPRETIFY_ASYNC=1 \
	-s EMTERPRETIFY_WHITELIST=@$(DBX_PATH)/src/em.txt -s BINARYEN_TRAP_MODE="js" -s ERROR_ON_UNDEFINED_SYMBOLS=0 $(TARGET) -o $(basename $(TARGET)).html 
endif