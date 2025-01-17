# modelbox.FlowUnit

|函数|作用|
|-|-|
|[open](#modelboxflowunitopen)|flowunit初始化逻辑|
|[process](#modelboxflowunitprocess)|flowunit处理逻辑|
|[close](#modelboxflowunitclose)|flowunit关闭逻辑|
|[data_pre](#modelboxflowunitdatapre)|stream flowunit 初始化stream时的逻辑|
|[data_post](#modelboxflowunitdatapost)|stream flowunit 结束stream时的逻辑|
|[data_group_pre](#modelboxflowunitdatagrouppre)|stream flowunit 初始化所有stream data前的逻辑(不用处理)|
|[data_group_post](#modelboxflowunitdatagrouppost)|stream flowunit 结束所有stream data后的逻辑(不用处理)|
|[create_external_data](#modelboxflowunitcreateexternaldata)|创建external_data_map|
|[get_bind_device](#modelboxflowunitgetbinddevice)|获取绑定的device|
|[create_buffer](#modelboxflowunitcreatebuffer)|创建buffer|
---

## modelbox.FlowUnit.open

flowunit初始化逻辑

**args:**  

* **config** (modelbox.Configuration) ——  流程图当中配给当前flowunit的配置项

**return:**  

modelbox.Status  初始化flowunit的返回状态

## modelbox.FlowUnit.process

flowunit处理逻辑

**args:**  

* **data_context**  (modelbox.DataContext) ——  处理逻辑当中存放数据的上下文

**return:**  

modelbox.Status flowunit处理逻辑的返回状态

## modelbox.FlowUnit.close

flowunit 结束逻辑

**args:**  

无

**return:**  

modelbox.Status flowunit 结束逻辑的返回状态

## modelbox.FlowUnit.data_pre

stream flowunit 初始化stream时的逻辑

**args:**  

* **data_context**  (modelbox.DataContext) ——  初始化stream逻辑当中存放数据的上下文

**return:**  

modelbox.Status 初始化stream时的逻辑的返回状态

## modelbox.FlowUnit.data_post

stream flowunit 结束stream时的逻辑

**args:**  

* **data_context**  (modelbox.DataContext) ——  结束stream逻辑当中存放数据的上下文

**return:**  

modelbox.Status 结束stream逻辑的返回状态

## modelbox.FlowUnit.data_group_pre

stream flowunit 初始化所有stream data前的逻辑

**args:**  

* **data_context**  (modelbox.DataContext) ——  初始化所有stream data前当中存放数据的上下文

**return:**  

modelbox.Status 初始化所有stream data前的返回状态

## modelbox.FlowUnit.data_group_post

stream flowunit 结束所有stream data后的逻辑

**args:**  

* **data_context**  (modelbox.DataContext) ——  结束所有stream data后当中存放数据的上下文

**return:**  

modelbox.Status 结束所有stream data后的逻辑的返回状态

**example:**  

```python
   ...
   # 典型flowunit场景
   class ExampleFlowUnit(modelbox.FlowUnit):
    # Derived from modelbox.FlowUnit
    def __init__(self):
        super().__init__()

    def open(self, config):
        # Open the flowunit to obtain configuration information
        return modelbox.Status.StatusCode.STATUS_SUCCESS

    def process(self, data_context):
        # Process the data
        in_data = data_context.input("in_1")
        out_data = data_context.output("out_1")

        # Example process code.
        # Remove the following code and add your own code here.
        for buffer in in_data:
            response = "Hello World " + buffer.as_object()
            result = response.encode('utf-8').strip()
            add_buffer = modelbox.Buffer(self.get_bind_device(), result)
            out_data.push_back(add_buffer)

        return modelbox.Status.StatusCode.STATUS_SUCCESS

    def close(self):
        # Close the flowunit
        return modelbox.Status()

    def data_pre(self, data_context):
        # Before streaming data starts
        return modelbox.Status()

    def data_post(self, data_context):
        # After streaming data ends
        return modelbox.Status()

    def data_group_pre(self, data_context):
        # Before all streaming data starts
        return modelbox.Status()

    def data_group_post(self, data_context):
        # After all streaming data ends
        return modelbox.Status()
        
```

```toml
   [base]
   name = "Example" # The FlowUnit name
   device = "cpu" # The device the flowunit runs on，cpu，cuda，ascend。
   version = "1.0.0" # The version of the flowunit
   description = "description" # The description of the flowunit
   entry = "example@ExampleFlowUnit" # Python flowunit entry function
   type = "python" # Fixed value

   # Flowunit Type
   stream = false # Whether the flowunit is a stream flowunit
   condition  = false # Whether the flowunit is a condition flowunit
   collapse = false # Whether the flowunit is a collapse flowunit
   collapse_all = false # Whether the flowunit will collapse all the data
   expand = false #  Whether the flowunit is a expand flowunit

   # The default Flowunit config
   [config]
   item = "value"

   # Input ports description
   [input]
   [input.input1] # Input port number, the format is input.input[N]
   name = "in_1" # Input port name
   type = "string" # Input port type

   # Output ports description
   [output]
   [output.output1] # Output port number, the format is output.output[N]
   name = "out_1" # Output port name
   type = "string" # Output port type
```

## modelbox.FlowUnit.create_external_data

创建external_data

**args:**  

无

**return:**  

modelbox.ExternalData, 创建好的external_data

**example:**  

```python
   ...
   def open(self, config):
       extern_data = self.create_external_data()
       ...
       return modelbox.Status()
```

**result:**  

可以参考external data的接口

## modelbox.FlowUnit.get_bind_device

获取当前flowunit绑定的device

**args:**  

无

**return:**  

modelbox.Device, 当前绑定的device

**example:**  

```python
   ...
   def process(self, data_context):
       device = self.get_bind_device()
       print(type(device))
       ...
       return modelbox.Status()
```

**result:**  

modelbox.Device

## modelbox.FlowUnit.create_buffer

创建buffer

**args:**  

无

**return:**  

modelbox.Buffer  创建出来的buffer

**example:**  

```python
   ...
   def process(self, data_context):
       e_array = np.array([])
       e_buffer = self.create_buffer(e_array)
       ...
       return modelbox.Status()
```

**result:**  

创建了一个空buffer
