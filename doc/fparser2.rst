..  Copyright (c) 2017-2018 Science and Technology Facilities Council.

    All rights reserved.

    Modifications made as part of the fparser project are distributed
    under the following license:

    Redistribution and use in source and binary forms, with or without
    modification, are permitted provided that the following conditions are
    met:

    1. Redistributions of source code must retain the above copyright
    notice, this list of conditions and the following disclaimer.

    2. Redistributions in binary form must reproduce the above copyright
    notice, this list of conditions and the following disclaimer in the
    documentation and/or other materials provided with the distribution.

    3. Neither the name of the copyright holder nor the names of its
    contributors may be used to endorse or promote products derived from
    this software without specific prior written permission.

    THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
    "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
    LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
    A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
    HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
    SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
    LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
    DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
    THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
    (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
    OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

.. _fparser2 :

fparser2
========

Fparser2 provides support for parsing Fortran up to and including
Fortran 2003 through the `Fortran2003` module. This is implemented in
the Fortran2003.py `file`__ and contains an entirely separate parser
that includes rules for Fortran 2003 syntax. Support for Fortran 2008
is being added in the Fortran2008.py `file`__ which extends the
Fortran2003 rules appropriately. At this time Fortran2008 support is
limited to submodules.

__ https://github.com/stfc/fparser/blob/master/src/fparser/two/Fortran2003.py
__ https://github.com/stfc/fparser/blob/master/src/fparser/two/Fortran2008.py


Getting Going
-------------

As with the other parser (:ref:`fparser`), the source code to parse
must be provided via an iterator which is an instance of either
`FortranFileReader` or `FortranStringReader` (see
:ref:`readfortran`). For example:

::
   
    >>> from fparser.two.parser import ParserFactory
    >>> from fparser.common.readfortran import FortranFileReader
    >>> reader = FortranFileReader("compute_unew_mod.f90",
                                   ignore_comments=False)
    >>> f2008_parser = ParserFactory().create(std="f2008")
    >>> program = f2008_parser(reader)
    >>> print program
    MODULE compute_unew_mod
      USE :: kind_params_mod
      USE :: kernel_mod
      USE :: argument_mod
      USE :: grid_mod
      USE :: field_mod
      IMPLICIT NONE
      PRIVATE
      PUBLIC :: invoke_compute_unew
      PUBLIC :: compute_unew, compute_unew_code
      TYPE, EXTENDS(kernel_type) :: compute_unew
      ...
    >>> program
    Program(Module(Module_Stmt('MODULE', Name('compute_unew_mod')), Specification_Part(Use_Stmt(None, Name('kind_params_mod'), '', None), Use_Stmt(None, Name('kernel_mod'), '', None), Use_Stmt(None, Name('argument_mod'), '', None), Use_Stmt(None, Name('grid_mod'), '', None), Use_Stmt(None, Name('field_mod'), '', None), Implicit_Part(Implicit_Stmt('NONE')), Access_Stmt('PRIVATE', None), Access_Stmt('PUBLIC', Name('invoke_compute_unew')), Access_Stmt('PUBLIC', Access_Id_List(',', (Name('compute_unew'), Name('compute_unew_code')))), Derived_Type_Def(Derived_Type_Stmt(Type_Attr_Spec('EXTENDS', Name('kernel_type')), Type_Name('compute_unew'), None), ...

The `ParserFactory` either returns a Fortran2003-compliant parser or a
Fortran2008-compliant parser depending on the `std` argument provided
to its create method.

Note that the two readers will ignore (and dispose of) comments by
default. If you wish comments to be retained then you must set
`ignore_comments=True` when creating the reader. The Abstract Syntax
Tree created by fparser2 will then have `Comment` nodes representing
any comments found in the code. Nodes representing in-line comments
will be added immediately following the node representing the code in
which they were encountered.

Classes
-------

.. autoclass:: fparser.common.readfortran.FortranFileReader
    :members:


.. autoclass:: fparser.common.readfortran.FortranStringReader
    :members:


.. autoclass:: fparser.two.parser.ParserFactory
    :members:


Data Model
----------

The module provides the classes; `Comment`, `Main_Program`,
`Subroutine_Subprogram`, `Function_Subprogram`, `Program_Stmt`,
`Function_Stmt`, `Subroutine_Stmt`, `Block_Do_Construct`,
`Block_Label_Do_Construct`, `Block_Nonlabel_Do_Construct`,
`Execution_Part`, `Name` and `Constant`, amongst others.  Nodes in the
tree representing the parsed code are instances of either `BlockBase`
or `SequenceBase`. Child nodes are then stored in the `.content`
attribute of `BlockBase` objects or the `.items` attribute of
`SequenceBase` objects. Both of these attributes are Tuple instances.
