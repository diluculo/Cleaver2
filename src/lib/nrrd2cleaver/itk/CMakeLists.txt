#-------------------------------------------------------------------
#
#  Copyright (C) 2015
#  Scientific Computing & Imaging Institute
#  University of Utah
#
#  Permission is  hereby  granted, free  of charge, to any person
#  obtaining a copy of this software and associated documentation
#  files  ( the "Software" ),  to  deal in  the  Software without
#  restriction, including  without limitation the rights to  use,
#  copy, modify,  merge, publish, distribute, sublicense,  and/or
#  sell copies of the Software, and to permit persons to whom the
#  Software is  furnished  to do  so,  subject  to  the following
#  conditions:
#
#  The above  copyright notice  and  this permission notice shall
#  be included  in  all copies  or  substantial  portions  of the
#  Software.
#
#  THE SOFTWARE IS  PROVIDED  "AS IS",  WITHOUT  WARRANTY  OF ANY
#  KIND,  EXPRESS OR IMPLIED, INCLUDING  BUT NOT  LIMITED  TO THE
#  WARRANTIES   OF  MERCHANTABILITY,  FITNESS  FOR  A  PARTICULAR
#  PURPOSE AND NONINFRINGEMENT. IN NO EVENT  SHALL THE AUTHORS OR
#  COPYRIGHT HOLDERS  BE  LIABLE FOR  ANY CLAIM, DAMAGES OR OTHER
#  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE,
#  ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE
#  USE OR OTHER DEALINGS IN THE SOFTWARE.
#-------------------------------------------------------------------
CMAKE_MINIMUM_REQUIRED(VERSION 2.6)
PROJECT(ITK_NRRD2Cleaver)

#Find ITK
FIND_PACKAGE(ITK COMPONENTS ITKCommon ITKIONRRD ITKIOMeta
  ITKThresholding ITKSmoothing ITKDistanceMap ITKIOImageBase REQUIRED)
IF (ITK_FOUND)
  INCLUDE (${ITK_USE_FILE})
ENDIF(ITK_FOUND)
MESSAGE(STATUS "** ITK_DIR: ${ITK_DIR}")

INCLUDE_DIRECTORIES(${CMAKE_SOURCE_DIR}/lib)
INCLUDE_DIRECTORIES(${CMAKE_SOURCE_DIR}/lib/nrrd2cleaver)

ADD_LIBRARY(nrrd2cleaver NRRDTools.cpp)

TARGET_LINK_LIBRARIES(nrrd2cleaver ${ITK_LIBRARIES} cleaver)
