---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/19/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: c01f57e2cfc9cd4351a39292258c554412eca555
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94974951"
---
<!--Create a basic audio transform REST-->

Il comando REST di Azure seguente consente di copiare video e audio da una posizione a un'altra senza ulteriore codifica. Sostituire i valori `subscriptionID` , `resourceGroup` e `accountName` con i valori attualmente in uso. Assegnare un nome alla trasformazione impostando `transformName` .

```REST

PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version=2020-05-01

```

## <a name="body"></a>Corpo

```json
{
    "properties": {
        "description": "Copy video and audio files with no encoding",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.CopyAudio"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.CopyVideo"
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "{Basename}_Copy{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="sample-response"></a>Risposta di esempio
```json
{
    "name": "aacaudio",
    "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/inhenkelRG/providers/Microsoft.Media/mediaservices/inhenkel/transforms/aacaudio",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "created": "2020-11-19T00:12:48.6001786Z",
        "description": "Copy audio files with no encoding",
        "lastModified": "2020-11-19T17:50:23.9602077Z",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.CopyAudio"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.CopyVideo"
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "{Basename}_Copy{Extension}",
                            "outputFiles": []
                        }
                    ]
                }
            }
        ]
    }
}
```