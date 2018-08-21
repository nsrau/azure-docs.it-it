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
ms.openlocfilehash: cdb60ffb3ba3c31c336c8b74c46621792c707f74
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2018
ms.locfileid: "40046378"
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