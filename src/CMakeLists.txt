cmake_minimum_required (VERSION 3.0)
include("GNUInstallDirs")
include("DetectArchitecture")

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)

find_package(Qt5 5.0.0 REQUIRED Widgets Xml XmlPatterns Svg)

if(EXISTS "${CMAKE_SOURCE_DIR}/.git")
	execute_process(
		COMMAND git rev-parse HEAD
		WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
		OUTPUT_VARIABLE GIT_BRANCH
		OUTPUT_STRIP_TRAILING_WHITESPACE
	)
else()
	set(GIT_BRANCH "Unknown")
endif()

add_definitions(-DGIT_BRANCH=${GIT_BRANCH})
add_definitions(-DDEFAULT_PLUGIN_PATH=\"${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}/edb\")

DETECT_ARCHITECTURE(CPU_TYPE)

set(edb_SRCS
	Address.cpp
	BasicBlock.cpp
	BinaryString.cpp
	BinaryString.ui
	ByteShiftArray.cpp
	CommentServer.cpp
	CommentServer.h
	Configuration.cpp
	DataViewInfo.cpp
	DataViewInfo.h
	DebugEventHandlers.cpp
	DebugEventHandlers.h
	Debugger.cpp
	Debugger.h
	Debugger.ui
	DebuggerInternal.h
	DialogAbout.cpp
	DialogAbout.h
	DialogAbout.ui
	DialogArguments.cpp
	DialogArguments.h
	DialogArguments.ui
	DialogAttach.cpp
	DialogAttach.h
	DialogAttach.ui
	DialogInputBinaryString.cpp
	DialogInputBinaryString.h
	DialogInputBinaryString.ui
	DialogInputValue.cpp
	DialogInputValue.h
	DialogInputValue.ui
	DialogMemoryRegions.cpp
	DialogMemoryRegions.h
	DialogMemoryRegions.ui
	DialogOpenProgram.cpp
	DialogOpenProgram.h
	DialogOptions.cpp
	DialogOptions.h
	DialogOptions.ui
	DialogPlugins.cpp
	DialogPlugins.h
	DialogPlugins.ui
	DialogThreads.cpp
	DialogThreads.h
	DialogThreads.ui
	ExpressionDialog.cpp
	ExpressionDialog.h
	FixedFontSelector.cpp
	FixedFontSelector.h
	FixedFontSelector.ui
	FloatX.cpp
	Function.cpp
	HexStringValidator.cpp
	MemoryRegions.cpp
	PluginModel.cpp
	PluginModel.h
	ProcessModel.cpp
	ProcessModel.h
	QLongValidator.cpp
	QULongValidator.cpp
	RecentFileManager.cpp
	RecentFileManager.h
	RegionBuffer.cpp
	RegionBuffer.h
	Register.cpp
	RegisterViewModelBase.cpp
	State.cpp
	SymbolManager.cpp
	SymbolManager.h
	ThreadsModel.cpp
	debugger.qrc
	edb.cpp
	main.cpp

	capstone-edb/Inspection.cpp
	capstone-edb/Instruction.cpp
	capstone-edb/include/Formatter.h
	capstone-edb/include/Inspection.h
	capstone-edb/include/Instruction.h
	capstone-edb/include/Operand.h
	qhexview/qhexview.cpp
	qhexview/qhexview.h
	qhexview/QHexView
	session/SessionError.cpp
	session/SessionError.h
	session/SessionManager.cpp
	session/SessionManager.h
	widgets/LineEdit.cpp
	widgets/LineEdit.h
	widgets/NavigationHistory.cpp
	widgets/NavigationHistory.h
	widgets/QDisassemblyView.cpp
	widgets/QDisassemblyView.h
	widgets/RegisterViewDelegate.cpp
	widgets/RegisterViewDelegate.h
	widgets/SyntaxHighlighter.cpp
	widgets/SyntaxHighlighter.h
	widgets/TabWidget.cpp
	widgets/TabWidget.h
	
	${PROJECT_SOURCE_DIR}/include/API.h
	${PROJECT_SOURCE_DIR}/include/ArchProcessor.h
	${PROJECT_SOURCE_DIR}/include/BasicBlock.h
	${PROJECT_SOURCE_DIR}/include/BinaryString.h
	${PROJECT_SOURCE_DIR}/include/ByteShiftArray.h
	${PROJECT_SOURCE_DIR}/include/Configuration.h
	${PROJECT_SOURCE_DIR}/include/edb.h
	${PROJECT_SOURCE_DIR}/include/Expression.h
	${PROJECT_SOURCE_DIR}/include/FloatX.h
	${PROJECT_SOURCE_DIR}/include/Function.h
	${PROJECT_SOURCE_DIR}/include/HexStringValidator.h
	${PROJECT_SOURCE_DIR}/include/IAnalyzer.h
	${PROJECT_SOURCE_DIR}/include/IBinary.h
	${PROJECT_SOURCE_DIR}/include/IBreakpoint.h
	${PROJECT_SOURCE_DIR}/include/IDebugEvent.h
	${PROJECT_SOURCE_DIR}/include/IDebugEventHandler.h
	${PROJECT_SOURCE_DIR}/include/IDebugger.h
	${PROJECT_SOURCE_DIR}/include/Instruction.h
	${PROJECT_SOURCE_DIR}/include/IPlugin.h
	${PROJECT_SOURCE_DIR}/include/IProcess.h
	${PROJECT_SOURCE_DIR}/include/IRegion.h
	${PROJECT_SOURCE_DIR}/include/IState.h
	${PROJECT_SOURCE_DIR}/include/ISymbolGenerator.h
	${PROJECT_SOURCE_DIR}/include/ISymbolManager.h
	${PROJECT_SOURCE_DIR}/include/IThread.h
	${PROJECT_SOURCE_DIR}/include/MemoryRegions.h
	${PROJECT_SOURCE_DIR}/include/Module.h
	${PROJECT_SOURCE_DIR}/include/Patch.h
	${PROJECT_SOURCE_DIR}/include/Prototype.h
	${PROJECT_SOURCE_DIR}/include/QtHelper.h
	${PROJECT_SOURCE_DIR}/include/QLongValidator.h
	${PROJECT_SOURCE_DIR}/include/QULongValidator.h
	${PROJECT_SOURCE_DIR}/include/Register.h
	${PROJECT_SOURCE_DIR}/include/RegisterRef.h
	${PROJECT_SOURCE_DIR}/include/RegisterViewModelBase.h
	${PROJECT_SOURCE_DIR}/include/ShiftBuffer.h
	${PROJECT_SOURCE_DIR}/include/State.h
	${PROJECT_SOURCE_DIR}/include/Status.h
	${PROJECT_SOURCE_DIR}/include/string_hash.h
	${PROJECT_SOURCE_DIR}/include/Symbol.h
	${PROJECT_SOURCE_DIR}/include/ThreadsModel.h
	${PROJECT_SOURCE_DIR}/include/Types.h
	${PROJECT_SOURCE_DIR}/include/Value.h
	${PROJECT_SOURCE_DIR}/include/Address.h
	${PROJECT_SOURCE_DIR}/include/Util.h
	${PROJECT_SOURCE_DIR}/include/version.h
	
	arch/${CPU_TYPE}-generic/ArchProcessor.cpp
	arch/${CPU_TYPE}-generic/RegisterViewModel.cpp
	${PROJECT_SOURCE_DIR}/include/arch/${CPU_TYPE}-generic/ArchTypes.h
)

if(UNIX)
	set(edb_SRCS
		${edb_SRCS}
		${PROJECT_SOURCE_DIR}/include/os/unix/OSTypes.h
	)
endif()

if(${CMAKE_SYSTEM_NAME} STREQUAL "Windows")
	enable_language(ASM_MASM)
	set(CMAKE_ASM_MASM_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /safeseh")

	set(edb_SRCS
		${edb_SRCS}
		LongDoubleX86.asm
		${PROJECT_SOURCE_DIR}/include/os/win32/OSTypes.h
	)
endif()

if(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
    set(edb_SRCS
        ${edb_SRCS}
        ${PROJECT_SOURCE_DIR}/include/os/unix/linux/linker.h
    )
endif()

if(${GRAPHVIZ_FOUND})
	set(edb_SRCS
		${edb_SRCS}
		graph/GraphEdge.cpp
		graph/GraphicsScene.cpp
		graph/GraphicsScene.h
		graph/GraphNode.cpp
		graph/GraphWidget.cpp
		${PROJECT_SOURCE_DIR}/include/GraphWidget.h
		${PROJECT_SOURCE_DIR}/include/GraphEdge.h
		${PROJECT_SOURCE_DIR}/include/GraphNode.h
	)
endif()

add_executable(edb ${edb_SRCS})

target_link_libraries(edb 
	${CAPSTONE_LIBRARIES} 
	Qt5::Widgets 
	Qt5::Xml 
	Qt5::XmlPatterns 
	Qt5::Svg 
	${GRAPHVIZ_LIBRARIES} 
	${DOUBLE_CONVERSION_LIBRARIES} 
	${GDTOA_LIBRARIES}
)

target_include_directories (edb PRIVATE
	"capstone-edb"
	"qhexview"
	"widgets"
	"session"
)

install (TARGETS edb DESTINATION ${CMAKE_INSTALL_BINDIR})

set_property(TARGET edb PROPERTY CXX_EXTENSIONS OFF)
set_property(TARGET edb PROPERTY CXX_STANDARD 14)
set_property(TARGET edb PROPERTY ENABLE_EXPORTS TRUE)
set_property(TARGET edb PROPERTY RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR})
