
# Makefile for aa

#------------------------------------------------------
include ../Makefile.inc
#------------------------------------------------------

SRC = src/
POT = pot/
OBJ = obj/
FALCONOBJ=$(OBJ)falcONobj/
#------------------------------------------------------
# Set paths to other code

GENERALPATH = ../general/
CTPATH = $(GENERALPATH)coordtransforms/
NEMOPATH = /data/jls/nemo/
FALCONPATH = $(NEMOPATH)usr/dehnen/falcON.P/
POTPATH = ../pot/

GNUPLOTLIBPATH = $(GENERALPATH)gnuplot
NUMRECLIBPATH = $(GENERALPATH)jamestools/numrec/
JAMESLIBPATH = $(GENERALPATH)jamestools/jamestools/
POTLIBPATH = ../pot/
CUBALIBPATH = -L$(GENERALPATH)cuba
CUBA = $(CUBALIBPATH) -lcuba

ifeq ($(TORUS), 1)
	TORUSINCPATH = -I$(TORUSPATH)/src/ -I$(TORUSPATH)/src/utils/ -I$(TORUSPATH)/src/pot
	TORUSLIBPATH = -L$(TORUSPATH)/obj/ -L$(TORUSPATH)/WDlib/obj/ -L$(EBFPATH)/lib/
	TORUSLIBS = -lTorus -lOther -lPot -lWD -lebf_cpp
else
	TORUSINCPATH =
	TORUSLIBPATH =
	TORUSLIBS =
endif

ifeq ($(LAPACK), 1)
	LAPACKPATH=$(LAPACKPATH)
	LAPACKLIBS=-llapack -lblas
else
	LAPACKLIBPATH=
	LAPACKLIBS=
endif
#------------------------------------------------------
# Set include and library paths

INC = -I../../ -I../ -Iinc/ $(TORUSINCPATH) -I$(GENERALPATH) -I$(GSLPATH)include/ -I$(POTPATH)inc/ -I$(CTPATH)inc/

INCPY = -I$(PYTHONINCPATH) -I$(BOOSTINCPATH)

LIBPATHS =
# -Wl,-rpath=$(POTLIBPATH)
# -Wl,-R$(GENERALPATH) -Wl,-R$(NUMRECLIBPATH) -Wl,-R$(JAMESLIBPATH)\
		   # -Wl,-R$(CUBALIBPATH) -Wl,-R$(GNUPLOTLIBPATH) -Wl,-R$(TORUSLIBPATH)\
		   # -Wl,-R$(GSLPATH)lib/ -Wl,-R$(CTPATH)

LIB = -Wl,-rpath,$(TORUSPATH)/obj/ -Wl,-rpath,$(TORUSPATH)/WDlib/obj/  -Wl,-rpath,$(EBFPATH)/lib/ -L$(GENERALPATH) -L$(NUMRECLIBPATH) -L$(JAMESLIBPATH) \
	$(CUBALIBPATH)\
	  -L$(GNUPLOTLIBPATH) $(TORUSLIBPATH) -L$(GSLPATH)lib/ -L$(POTLIBPATH)\
	  -L$(CTPATH) $(TORUSLIBS) -lpot_js -lcoords \
	  -lpress_cp -lplot -lgsl -lgslcblas -lm $(EXTRACLIB) $(LAPACKLIBPATH) -ljamestools $(LAPACKLIBS) $(TORUSFLAG) $(LAPACKFLAG) $(LIBPATHS)
BOOSTLIB = -lboost_python -lpython2.7
LIBPY = $(PYTHONLIBPATH) $(BOOSTLIBPATH) $(BOOSTLIB)

#------------------------------------------------------
# Additional stuff for falcON programs

FALCONINC = -I$(FALCONPATH)inc/ -I$(FALCONPATH)inc/public/ -I$(FALCONPATH)utils/inc/ -I$(NEMOPATH)inc/ -I$(NEMOPATH)inc/max/ -I$(NEMOPATH)lib/ -DfalcON_SINGLE -DfalcON_NEMO -DfalcON_PROPER

FALCONLIB = -L$(FALCONPATH)lib/ -lfalcON.P -L$(FALCONPATH)utils/lib/ -lWDutils -L$(NEMOPATH)lib/ -lnemo++ -lnemo -DfalcON_SINGLE -DfalcON_NEMO -DfalcON_PROPER

#------------------------------------------------------
# Set files

LIBRARY = libaa.so

OBJECTS = $(OBJ)utils.o $(OBJ)aa.o $(OBJ)debug.o $(OBJ)spherical_aa.o $(OBJ)stackel_aa.o  $(OBJ)get_closed_Rz.o $(OBJ)uv_orb.o $(OBJ)lmn_orb.o $(OBJ)stackel_fit.o $(OBJ)analytic_aa.o $(OBJ)genfunc_aa.o $(OBJ)adiabatic_aa.o $(OBJ)tables_aa.o
ifeq ($(TORUS), 1)
	OBJECTS += $(OBJ)it_torus.o
endif
HEADERS = $(wildcard inc/*.h)

#------------------------------------------------------

default: clean all

all: lib/libaa.so mains/test_actions.exe mains/test_angles.exe mains/many_tori.exe mains/genfunc_converg.exe

python: lib/aa_py.so

forceall: clean all

#------------------------------------------------------

$(OBJ)aa_py.o : $(SRC)aa_py.cpp ${HEADERS}
	$(CCOMPILER) $(OPTS) -c $< -o $@ $(INC) $(INCPY) $(TORUSFLAG) $(LAPACKFLAG)

$(OBJ)utils.o : ../general/utils.cpp ${HEADERS}
	$(CCOMPILER) $(OPTS) -c $< -o $@ $(INC) $(TORUSFLAG) $(LAPACKFLAG)

$(OBJ)%.o : $(SRC)%.cpp ${HEADERS}
	$(CCOMPILER) $(OPTS) -c $< -o $@ $(INC) $(TORUSFLAG) $(LAPACKFLAG)

$(OBJ)%.o : mains/%.cpp ${HEADERS}
	$(CCOMPILER) $(OPTS) -c $< -o $@ $(INC) $(TORUSFLAG) $(LAPACKFLAG)

$(FALCONOBJ)%.o : $(SRC)%.cpp
	$(CCOMPILER) $(OPTS) -c $< -o $@ $(INC) $(FALCONINC) $(TORUSFLAG)

#------------------------------------------------------

## TESTS
analytic_test.exe: $(OBJECTS)
	$(CCOMPILER) $(OPTS) -o analytic_test.exe $(OBJECTS) $(LIB)

genfunc_test.exe: $(OBJECTS)
	$(CCOMPILER) $(OPTS) -o genfunc_test.exe $(OBJECTS) $(LIB) $(CUBA)

test.exe: $(OBJECTS) $(OBJ)test.o
	$(CCOMPILER) $(OPTS) -o test.exe $(OBJECTS) $(OBJ)test.o $(INC) $(LIB) $(CUBA) $(TORUSFLAG) $(LAPACKFLAG)

mains/test_actions.exe: $(OBJECTS) $(OBJ)test_actions.o
	$(CCOMPILER) $(OPTS) -o mains/test_actions.exe $(OBJECTS) $(OBJ)test_actions.o $(LIB) $(CUBA) -static-libstdc++

mains/test_angles.exe: $(OBJECTS) $(OBJ)test_angles.o
	$(CCOMPILER) $(OPTS) -o mains/test_angles.exe $(OBJECTS) $(OBJ)test_angles.o $(LIB) $(CUBA) -static-libstdc++

mains/many_tori.exe: $(OBJECTS) $(OBJ)many_tori.o
	$(CCOMPILER) $(OPTS) -o mains/many_tori.exe $(OBJECTS) $(OBJ)many_tori.o $(LIB) $(CUBA)

mains/many_tori_james.exe: $(OBJECTS) $(OBJ)many_tori_james.o
	$(CCOMPILER) $(OPTS) -o mains/many_tori_james.exe $(OBJECTS) $(OBJ)many_tori_james.o $(LIB) $(CUBA)

mains/genfunc_converg.exe: $(OBJECTS) $(OBJ)genfunc_converg.o
	$(CCOMPILER) $(OPTS) -o mains/genfunc_converg.exe $(OBJECTS) $(OBJ)genfunc_converg.o $(LIB) $(CUBA)

spherical_test.exe: $(OBJECTS)
	$(CCOMPILER) $(OPTS) -o spherical_test.exe $(OBJECTS) $(LIB)

stackel_fit_test.exe: $(OBJECTS)
	$(CCOMPILER) $(OPTS) -o stackel_fit_test.exe $(OBJECTS) $(LIB) $(CUBA)

lmnnew_test.exe: $(OBJECTS)
	$(CCOMPILER) $(OPTS) -o lmnnew_test.exe $(OBJECTS) $(LIB) $(CUBA)
## LIBRARY

lib/libaa.so: $(OBJECTS)
	$(CCOMPILER) -fPIC $(LIBFLAG) $(CFL) $(OBJECTS) $(INC) -Wl,-$(LIBNAME),$(CURDIR)/lib/libaa.so  $(LIB) $(CUBA) -o lib/libaa.so

triax_py.so: $(OBJECTS) $(OBJ)triax_py.o
	$(CCOMPILER) -fPIC $(LIBFLAG) $(CFL) $(OBJECTS) $(OBJ)triax_py.o $(INC) $(LIB)  -o triax_py.so

lib/aa_py.so: $(OBJECTS) $(OBJ)aa_py.o
	$(CCOMPILER) -fPIC $(LIBFLAG) $(OPTS) $(OBJECTS) $(OBJ)aa_py.o $(INC) $(LIB) $(LIBPY) $(CUBA) -o lib/aa_py.so

## OTHER

falcON_aa.exe: $(OBJECTS) $(FALCONOBJ)falcON_aa.o
	$(CCOMPILER) $(OPTS) -o falcON_aa.exe $(OBJECTS) $(FALCONOBJ)falcON_aa.o $(FALCONLIB) $(LIB) $(LIBPY) $(CUBA)

.PHONY: clean
clean:
	rm -rf obj/*.o; rm -rf *.so; rm -rf mains/*.exe
