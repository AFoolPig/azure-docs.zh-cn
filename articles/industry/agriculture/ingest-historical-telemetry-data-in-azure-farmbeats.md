---
title: 引入历史遥测数据
description: 本文介绍如何引入历史遥测数据。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0d220d1d88d9d761d9f0eba6187abefb372681be
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131901"
---
# <a name="ingest-historical-telemetry-data"></a>引入历史遥测数据

本文介绍如何将历史传感器数据引入 Azure FarmBeats。

在 FarmBeats 中，引入（IoT）资源（如设备和传感器）的历史物联网数据是常见的方案。 为设备和传感器创建元数据，然后将历史数据以规范格式引入 FarmBeats。

## <a name="before-you-begin"></a>开始之前

在继续阅读本文之前，请确保已安装 FarmBeats 并从 IoT 设备收集了历史数据。
还需要启用合作伙伴访问权限，如以下步骤中所述。

## <a name="enable-partner-access"></a>启用合作伙伴访问

需要启用与 Azure FarmBeats 实例的合作伙伴集成。 此步骤将创建一个客户端，该客户端可以访问作为设备伙伴的 Azure FarmBeats 实例，并提供后续步骤中所需的以下值：

- API 终结点：这是 Datahub URL，例如 https://\<Datahub > appname>.azurewebsites.net。
- 租户 ID
- 客户端 ID
- 客户端机密
- EventHub 连接字符串

请执行下列步骤。

>[!NOTE]
> 您必须是管理员才能执行以下步骤。

1. 下载[zip 文件](https://aka.ms/farmbeatspartnerscriptv2)，并将其解压缩到本地驱动器。 Zip 文件中将有一个文件。
2. 登录到 https://portal.azure.com/ 并中转到 Azure Active Directory > 应用注册

3. 单击在 FarmBeats 部署过程中创建的应用注册。 它的名称与你的 FarmBeats Datahub 相同。

4. 单击 "公开 API"-> 单击 "添加客户端应用程序" 并输入**04b07795-8ddb-461a-bbee-02f9e1bf7b46**并选中 "授权范围"。 这将授予对 Azure CLI （Cloud Shell）的访问权限，以执行以下步骤。

5. 打开 Cloud Shell。 此选项位于 Azure 门户右上角工具栏中。

    ![Azure 门户工具栏](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. 请确保将环境设置为**PowerShell**。 默认情况下，它设置为 Bash。

    ![PowerShell 工具栏设置](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. 在 Cloud Shell 实例的步骤1中上传文件。

    ![上传工具栏按钮](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. 中转到上载文件的目录。 默认情况下，文件将上传到用户名下的主目录。

9. 运行以下脚本。 该脚本要求提供可从 Azure Active Directory > 概述页获取的租户 ID。

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. 按照屏幕上的说明来捕获**API 终结点**、**租户 ID**、**客户端 ID**、**客户端密钥**和**EventHub 连接字符串**的值。
## <a name="create-device-or-sensor-metadata"></a>创建设备或传感器元数据

 现在，你已拥有所需的凭据，可以定义设备和传感器。 为此，请通过调用 FarmBeats Api 来创建元数据。 请注意，你需要将 Api 作为你在上一节中创建的客户端应用程序调用

 FarmBeats Datahub 提供了以下 Api，可用于创建和管理设备或传感器元数据。 请注意，作为合作伙伴，你可以访问仅读取、创建和更新元数据;**伙伴不允许删除。**

- /**DeviceModel**： DeviceModel 对应于设备的元数据，如制造商和设备类型（网关或节点）。
- /**设备**：设备对应于在场上存在的物理设备。
- /**SensorModel**： SensorModel 对应于传感器的元数据，如制造商、传感器类型（模拟或数字）以及传感器度量，如环境温度和压力。
- /**传感器**：传感器对应于记录值的物理传感器。 传感器通常连接到设备 ID 为的设备。  


|        DeviceModel   |  建议   |
| ------- | -------             |
|     类型（节点、网关）        |          设备节点或网关的类型      |
|          制造商            |         制造商的名称    |
|  ProductCode                    |  设备产品代码或型号名称。 例如，EnviroMonitor # 6800。  |
|            端口          |     端口名称和类型，它是数字或模拟。
|     名称                 |  用于标识资源的名称。 例如，模型名称或产品名称。
      说明     | 提供模型的有意义的说明。
|    属性          |    制造商提供的其他属性。   |
|    **设备**             |                      |
|   DeviceModelId     |     关联的设备模型的 ID。  |
|  HardwareId          | 设备的唯一 ID，如 MAC 地址。
|  ReportingInterval        |   报告间隔（秒）。
|  Location            |  设备纬度（-90 到 + 90）、经度（-180 到180）和提升（以米为单位）。   
|ParentDeviceId       |    此设备连接到的父设备的 ID。 例如，连接到网关的节点。 节点将 parentDeviceId 作为网关。  |
|    名称            | 用于标识资源的名称。 设备合作伙伴必须发送与合作伙伴端设备名称一致的名称。 如果合作伙伴设备名称是用户定义的，则应将同一用户定义的名称传播到 FarmBeats。|
|     说明       |      提供有意义的说明。 |
|     属性    |  制造商提供的其他属性。
|     **SensorModel**        |          |
|       类型（模拟、数字）          |      传感器类型，无论是模拟还是数字。       |
|          制造商            |       传感器的制造商。     |
|     ProductCode| 产品代码或型号名称。 例如，RS-N01。 |
|       SensorMeasures > 名称       | 传感器度量值的名称。 仅支持小写。 对于不同深度的度量，请指定深度。 例如，soil_moisture_15cm。 此名称必须与遥测数据一致。  |
|          SensorMeasures > 数据类型       |遥测数据类型。 目前支持 double。|
|    SensorMeasures > 类型    |传感器遥测数据的度量类型。 系统定义类型为 AmbientTemperature、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、压力、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR。 若要添加更多，请参阅/ExtendedType API。|
|        SensorMeasures > 单元              | 传感器遥测数据的单位。 系统定义的单位为 NoUnit、摄氏度、华氏度、开氏度、Rankine、Pascal、水星、PSI、毫米、厘米、米、英寸、英尺、英里、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、WattsPerSquareMeter、KiloWattsPerSquareMeter、百分比、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、PartsPerMillion、MilliSiemensPerCentiMeter，Centibar，DeciSiemensPerMeter，KiloPascal，VolumetricIonContent，升，MilliLiter，Seconds，UnixTimestamp，MicroMolPerMeterSquaredPerSecond，InchesPerHour 若要添加更多，请参阅/ExtendedType API。|
|    SensorMeasures > AggregationType    |  值可以为 "无"、"平均值"、"最大值" 或 "StandardDeviation"。  |
|          名称            | 用于标识资源的名称。 例如，模型名称或产品名称。  |
|    说明        | 提供模型的有意义的说明。  |
|   属性       |  制造商提供的其他属性。  |
|    **器**      |          |
| HardwareId          |   制造商设置的传感器的唯一 ID。 |
|  SensorModelId     |    关联的传感器型号的 ID。   |
| Location          |  传感器纬度（-90 到 + 90）、经度（-180 到180）和提升（以米为单位）。|
|   端口 > 名称        |  设备上传感器连接到的端口的名称和类型。 此名称需要与设备模型中定义的名称相同。 |
|    设备 ID  |    传感器连接到的设备的 ID。     |
| 名称            |   标识资源的名称。 例如，传感器名称或产品名称以及型号或产品代码。|
|    说明      | 提供有意义的说明。 |
|    属性        |制造商提供的其他属性。 |

有关对象的详细信息，请参阅[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

### <a name="api-request-to-create-metadata"></a>用于创建元数据的 API 请求

若要发出 API 请求，请将 HTTP （POST）方法、API 服务的 URL 和用于查询、将数据提交到、创建或删除请求的 URI 组合在一起。 然后添加一个或多个 HTTP 请求标头。 API 服务的 URL 是 API 终结点，即 Datahub URL （ https://\<yourdatahub > appname>.azurewebsites.net）。  

### <a name="authentication"></a>身份验证

FarmBeats Datahub 使用持有者身份验证，该身份验证需要在上一节中生成的以下凭据：

- 客户端 ID
- 客户端机密
- 租户 ID

使用这些凭据，调用方可以请求访问令牌。 令牌必须在后续 API 请求的标头部分中发送，如下所示：

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

下面的示例 Python 代码提供访问令牌，该令牌可用于对 FarmBeats 的后续 API 调用： 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**HTTP 请求标头**

下面是在对 FarmBeats Datahub 进行 API 调用时必须指定的最常见的请求标头：

- **Content-type**： application/json
- **授权**：持有者 < 访问令牌 >
- **接受**： application/json

### <a name="input-payload-to-create-metadata"></a>用于创建元数据的输入有效负载

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

设备

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
传感器

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
以下示例请求创建设备。 此请求将输入 JSON 作为带有请求正文的有效负载。

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> Api 为创建的每个实例返回唯一 Id。 必须保留 Id 才能发送相应的遥测消息。

### <a name="send-telemetry"></a>发送遥测

现在，你已在 FarmBeats 中创建了设备和传感器，接下来可以发送关联的遥测消息。

### <a name="create-a-telemetry-client"></a>创建遥测客户端

必须将遥测发送到 Azure 事件中心进行处理。 Azure 事件中心是一项服务，可用于从连接的设备和应用程序引入实时数据（遥测）。 若要将遥测数据发送到 FarmBeats，请创建将消息发送到 FarmBeats 中的事件中心的客户端。 有关发送遥测数据的详细信息，请参阅[Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)。

### <a name="send-a-telemetry-message-as-the-client"></a>将遥测消息作为客户端发送

建立作为事件中心客户端的连接后，可以将消息作为 JSON 发送到事件中心。

下面是将遥测作为客户端发送到指定事件中心的示例 Python 代码：

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

将历史传感器数据格式转换为 Azure FarmBeats 了解的规范格式。 规范消息格式如下所示：

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

添加相应的设备和传感器之后，在遥测消息中获取设备 ID 和传感器 ID，如前一部分中所述。

下面是遥测消息的示例：


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```

## <a name="troubleshooting"></a>故障排除

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>从传感器引入历史/流式传输数据后，无法查看遥测数据

**症状**：设备或传感器已部署，并且你已在 FarmBeats 和引入遥测上为 EventHub 创建了设备/传感器，但无法在 FarmBeats 上获取或查看遥测数据。

**纠正操作**：

1. 请确保已正确完成合作伙伴注册-可以通过转到 datahub swagger 来检查此项，导航到/Partner API，执行 Get 操作并检查伙伴是否已注册。 否则，请按照此处的[步骤](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)添加合作伙伴。
2. 确保已使用合作伙伴客户端凭据创建元数据（DeviceModel、设备、SensorModel、传感器）。
3. 确保使用了正确的遥测消息格式（如下所示）：

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```


## <a name="next-steps"></a>后续步骤

有关基于 REST API 的集成详细信息的详细信息，请参阅[REST API](rest-api-in-azure-farmbeats.md)。
