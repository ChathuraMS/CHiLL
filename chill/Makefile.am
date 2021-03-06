SUBDIRS = omega/omega_lib/obj omega/code_gen/obj
build_date = "\"`date +%m/%d/%Y`\""

## Core Libraries ##
core_libs     = -lm -lrose -lrt -lutil -lomega -lcodegen -ldl
core_libs    += -lboost_date_time -lboost_filesystem -lboost_program_options
core_libs    += -lboost_regex -lboost_system -lboost_thread -lboost_wave -lboost_iostreams

core_libdirs  = -Lomega/code_gen/obj -Lomega/omega_lib/obj
core_libdirs += -L$(ROSEHOME)/lib -L$(BOOSTHOME)/lib



## Core Includes ##
core_includes  = -Iomega/include -I$(ROSEHOME)/include
core_includes += -I$(BOOSTHOME)/include


## Source ##
# Core #
core_src  = dep.cc
core_src += irtools.cc
core_src += loop.cc
core_src += loop_basic.cc
core_src += loop_datacopy.cc
core_src += loop_extra.cc
core_src += loop_tile.cc
core_src += loop_unroll.cc
core_src += omegatools.cc

# chill #
# cudachill #
cudachill_src  = mem_mapping_utils.cc
cudachill_src += loop_cuda_rose.cc

# IR #
#  chill
ir_chill_src      = ir_rose.cc
ir_chill_src     += ir_rose_utils.cc

#  cudachill
ir_cudachill_src  = ir_rose.cc
ir_cudachill_src += ir_rose_utils.cc
ir_cudachill_src += ir_cudarose.cc
ir_cudachill_src += ir_cuda_rose_utils.cc

# runner (interface) #
#  cc
chill_script_runner_src     = 
chill_python_runner_src     = chill_run.cc chill_run_util.cc chillmodule.cc
chill_lua_runner_src        = chill_run.cc chill_run_util.cc chill_env.cc
#  yacc
chill_script_yacc_src       = lex.yy.cc parser.tab.cc
chill_lua_yacc_src          = parse_expr.yy.cc parse_expr.tab.cc
chill_python_yacc_src       = parse_expr.yy.cc parse_expr.tab.cc



## Interface ##
# Libs/Source/Flags #
if PYTHON_OPT
runner_libs           = -lpython$(PYTHON_VERSION)
runner_libdirs        = -L$(PYTHON_LIBDIR)
runner_includes       = -I$(PYTHON_INCDIR)
chill_runner_src      = $(chill_python_runner_src) $(chill_python_yacc_src)
cudachill_runner_src  = $(chill_python_runner_src) $(chill_python_yacc_src)
yacc_src              = $(chill_python_yacc_src)
runner_flags          = -DCHILL_BUILD_DATE=$(build_date)
runner_flags         += -DCHILL_BUILD_VERSION=$(CHILL_BUILD_VERSION)
runner_flags         += -DPYTHON
else
if LUA_OPT
runner_libs           = -llua -lreadline -lhistory -lpthread -ldl
runner_libdirs        = -L$(LUAHOME)/lib
runner_includes       = -I$(LUAHOME)/include
chill_runner_src      = $(chill_lua_runner_src) $(chill_lua_yacc_src)
cudachill_runner_src  = $(chill_lua_runner_src) $(chill_lua_yacc_src)
yacc_src              = $(chill_lua_yacc_src)
runner_flags          = -DCHILL_BUILD_DATE=$(build_date)
runner_flags         += -DCHILL_BUILD_VERSION=$(CHILL_BUILD_VERSION)
runner_flags         += -DLUA
else
runner_libs           =
runner_libdirs        =
runner_includes       =
chill_runner_src      = $(chill_script_runner_src) $(chill_script_yacc_src)
yacc_src              = $(chill_script_yacc_src)
runner_flags          = -DCHILL_BUILD_DATE=$(build_date)
runner_flags         += -DCHILL_BUILD_VERSION=$(CHILL_BUILD_VERSION)
endif
endif



## Yacc ##
# config
BUILT_SOURCES=$(yacc_src)
AM_YFLAGS=-d -t

# rules
lex.yy.cc: parser.ll parser.tab.cc
	flex++ parser.ll

parser.tab.cc: parser.yy
	bison -t -d $<

parse_expr.yy.cc: parse_expr.tab.cc parse_expr.ll
	flex -o parse_expr.yy.cc parse_expr.ll

parse_expr.tab.cc: parse_expr.yy
	bison -t -d parse_expr.yy



## Common ##
common_cppflags  = $(core_includes) $(runner_includes)
common_cppflags += $(runner_flags)
common_cppflags += -DBUILD_ROSE
common_libs      = $(core_libs) $(runner_libs)
common_libdirs   = $(core_libdirs) $(runner_libdirs)

## Binaries ##
if CUDACHILL_OPT
bin_PROGRAMS=cudachill
else
bin_PROGRAMS=chill
endif

cudachill_CPPFLAGS  =-DCUDACHILL $(common_cppflags)
cudachill_LDADD     =$(common_libs)
cudachill_LDFLAGS   =$(common_libdirs)
cudachill_SOURCES   =$(core_src) $(cudachill_runner_src) $(cudachill_src) $(ir_cudachill_src)

chill_CPPFLAGS  =$(common_cppflags) $(chill_runner_cppflags)
chill_LDADD     =$(common_libs)
chill_LDFLAGS   =$(common_libdirs)
chill_SOURCES   =$(core_src) $(chill_runner_src) $(chill_src) $(ir_chill_src)

