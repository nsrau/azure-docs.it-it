---
title: File di inclusione
description: File di inclusione
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 2b61cc8c5c448c28e96b06afa3556688a82567ed
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202689"
---
### <a name="delete-local-resources"></a>Eliminare le risorse locali

Se si vogliono rimuovere il runtime IoT Edge e le risorse correlate dal dispositivo, usare i comandi appropriati per il sistema operativo del dispositivo. 

#### <a name="windows"></a>Windows

Disinstallare il runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

Quando il runtime IoT Edge viene rimosso, i contenitori creati vengono arrestati, ma rimangono sul dispositivo. Visualizzare tutti i contenitori.

   ```powershell
   docker ps -a
   ```

Eliminare i contenitori di runtime creati nel dispositivo.

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Eliminare gli eventuali altri contenitori elencati nell'output di `docker ps` facendo riferimento ai nomi dei contenitori. 

#### <a name="linux"></a>Linux

Rimuovere il runtime IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Quando il runtime IoT Edge viene rimosso, i contenitori creati vengono arrestati, ma rimangono sul dispositivo. Visualizzare tutti i contenitori.

   ```bash
   sudo docker ps -a
   ```

Eliminare i contenitori di runtime creati nel dispositivo.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Eliminare gli eventuali altri contenitori elencati nell'output di `docker ps` facendo riferimento ai nomi dei contenitori. 

Rimuovere il runtime del contenitore.

   ```bash
   sudo apt-get remove --purge moby
   ```