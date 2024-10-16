.. default-domain:: chpl

.. module:: Network

Network
=======
**Usage**

.. code-block:: chapel

   use Network;


or

.. code-block:: chapel

   import Network;

.. function:: proc helpFindModuleByName(arg, x: string): borrowed(Module(?))

.. function:: proc helpFindParamDataByName(arg, x: string) ref: Tensor(?)

.. record:: moduleChildren

   .. attribute:: type eltType = real

   .. attribute:: var childDict: map(string, borrowed(Module(eltType)))

   .. attribute:: var order: list(string)

   .. method:: proc init(type eltType = real)

   .. itermethod:: iter ref these(): borrowed(Module(eltType))

   .. itermethod:: iter ref items(): (string, borrowed(Module(eltType)))

   .. method:: proc ref add(name: string, m: borrowed(Module(eltType)))

   .. method:: proc ref ith(i: int): borrowed(Module(eltType))

.. method:: proc class.this(fieldName: string): borrowed(Module(?)) where isSubtype(this.type, Module(?))

.. method:: proc class.this(fieldName: string) ref: Tensor(?) where isSubtype(this.type, Parameter(?))

.. itermethod:: iter class.moduleFieldNames(): string where isSubtype(this.type, Module(?))

.. itermethod:: iter class.moduleFields(): (string, borrowed(Module(?))) where isSubtype(this.type, Module(?))

.. method:: proc class.registerModules() where isSubtype(this.type, Module(?))

.. method:: proc class.postinit() where isSubtype(this.type, Module(?))

.. record:: moduleAttributes : serializable

   .. attribute:: var layerType: string

   .. attribute:: var moduleName: string

   .. attribute:: var attributes: dict(string, string)

   .. method:: proc init(layerType: string, moduleName: string, in attrs: map(string, string, ?))

   .. method:: proc init(layerType: string, moduleName: string, in attrs: dict(string, ?))

   .. method:: proc init(layerType: string, moduleName: string, in attrs: map(string, ?valType, ?)) where valType != string

   .. method:: proc init(layerType: string, moduleName: string, in attrs: map(string, ?valType, ?), order: list(string))

   .. method:: proc init(layerType: string, moduleName: string, attrs ...?n) where attrs(0)(0).type == string

   .. method:: proc init(layerType: string, moduleName: string)

   .. method:: proc getInt(name: string): int

   .. method:: proc prettyPrint(): string

   .. method:: proc prettyPrintSpec(): string

   .. method:: operator :(ma: moduleAttributes, type T: string)

.. class:: ModuleSpecification : serializable

   .. attribute:: var layerType: string

   .. attribute:: var attributes: map(string, string)

   .. attribute:: var subModules: map(string, owned(ModuleSpecification?))

   .. attribute:: var subModuleOrder: list(string)

.. function:: proc moduleFromSpec(ms_: borrowed(ModuleSpecification?), type dtype = real(32)): owned(Module(dtype))

.. function:: proc modelFromSpecFile(path: string, type dtype = real(32)): owned(Module(dtype))

.. function:: proc loadModel(specFile: string, weightsFolder: string, type dtype = real(32)): owned(Module(dtype))

.. data:: var moduleInstances = 0

.. class:: Module

   .. attribute:: type eltType

   .. attribute:: var subModules: moduleChildren(eltType)

   .. attribute:: var moduleId: int

   .. attribute:: var moduleName: string

   .. attribute:: var ownedModules: list(shared(Module(eltType)))

   .. method:: proc init(type eltType = real)

   .. method:: proc init(type eltType = real, ma: moduleAttributes)

   .. method:: proc setup()

   .. method:: proc this(input: Tensor(eltType)): Tensor(eltType)

   .. method:: proc getSubModuleName(name: string): string

   .. method:: proc addModule(name: string, m: borrowed(Module(eltType)))

   .. method:: proc addModule(name: string, m: shared(Module(eltType)))

   .. method:: proc addModule(name: string, in m: owned(Module(eltType)))

   .. method:: proc addParameter(name: string, data: Tensor(eltType))

   .. method:: proc forward(input: Tensor(eltType)): Tensor(eltType)

   .. method:: proc par(paramName: string) ref: Tensor(eltType)

   .. method:: proc mod(modName: string): borrowed(Module(eltType))

   .. itermethod:: iter parameters(): borrowed(Parameter(eltType))

   .. itermethod:: iter moduleNames(): string

   .. itermethod:: iter parameterNames(): string

   .. itermethod:: iter modules(): borrowed(Module(eltType))

   .. itermethod:: iter namedModules(): (string, borrowed(Module(eltType)))

   .. method:: proc loadPyTorchDump(modelPath: string, param debug = false)

   .. method:: proc attributes(): moduleAttributes

   .. method:: proc signature: string

.. class:: Parameter : Module(?)

   .. attribute:: var data: Tensor(eltType)

   .. method:: proc init(data: Tensor(?eltType))

   .. method:: override proc attributes(): moduleAttributes

.. class:: Sequential : Module(?)

   .. attribute:: var mds: list(shared(Module(eltType)))

   .. method:: proc init(type eltType = real, ms: dict(string, shared(Module(eltType))), param overrideName = false, moduleName: string = "")

   .. method:: proc init(type eltType = real, in ms)

   .. method:: proc init(type eltType = real, order: list(string), in ms: map(string, owned(Module(eltType)?)))

   .. method:: proc init(in ms: owned Module(real)? ...?rank)

   .. method:: override proc forward(input: Tensor(eltType)): Tensor(eltType)

   .. method:: override proc attributes(): moduleAttributes

.. class:: Linear : Module(?)

   .. attribute:: var m: int

   .. attribute:: var n: m.type

   .. attribute:: var weight: owned(Parameter(eltType))

   .. attribute:: var bias: owned(Parameter(eltType))

   .. method:: proc init(type eltType, m: int, n: int)

   .. method:: override proc setup()

   .. method:: proc init(m: int, n: int)

   .. method:: override proc forward(input: Tensor(eltType)): Tensor(eltType)

   .. method:: override proc attributes(): moduleAttributes

.. class:: Conv2D : Module(?)

   .. attribute:: var kernelShape: 4*(int)

   .. attribute:: var stride: int

   .. attribute:: var kernel: owned(Parameter(eltType))

   .. attribute:: var bias: owned(Parameter(eltType))

   .. method:: proc init(type eltType = real, channels: int, features: int, kernel: int, stride: int = 1)

   .. method:: proc init(type eltType = real, ma: moduleAttributes)

   .. method:: override proc setup()

   .. method:: proc init(channels: int, features: int, kernel: int, stride: int = 1)

   .. method:: override proc forward(input: Tensor(eltType)): Tensor(eltType)

   .. method:: override proc attributes(): moduleAttributes

.. class:: MaxPool : Module(?)

   .. attribute:: var poolSize: int

   .. method:: proc init(type eltType = real, poolSize: int)

   .. method:: proc init(poolSize: int)

   .. method:: override proc forward(input: Tensor(eltType)): Tensor(eltType)

   .. method:: override proc attributes(): moduleAttributes

.. class:: Flatten : Module(?)

   .. method:: proc init(type eltType = real)

   .. method:: override proc forward(input: Tensor(eltType)): Tensor(eltType)

   .. method:: override proc attributes(): moduleAttributes

.. class:: ReLU : Module(?)

   .. method:: proc init(type eltType = real)

   .. method:: override proc forward(input: Tensor(eltType)): Tensor(eltType)

   .. method:: override proc attributes(): moduleAttributes

.. class:: Softmax : Module(?)

   .. method:: proc init(type eltType = real)

   .. method:: override proc forward(input: Tensor(eltType)): Tensor(eltType)

   .. method:: override proc attributes(): moduleAttributes

.. class:: Dropout : Module(?)

   .. method:: proc init(type eltType = real, freq: real = 0.5)

   .. method:: override proc forward(input: Tensor(eltType)): Tensor(eltType)

   .. method:: override proc attributes(): moduleAttributes

.. function:: proc chain(m: borrowed(Module(?)), modNames: string ...?n, input: Tensor(?eltType))

.. class:: Net : Module(?)

   .. method:: proc init(type eltType = real)

   .. method:: override proc forward(input: Tensor(eltType)): Tensor(eltType)

.. function:: proc main()

