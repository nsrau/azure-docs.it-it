---
title: Immagini Red Hat Enterprise Linux in Azure | Microsoft Docs
description: Informazioni sulle immagini Red Hat Enterprise Linux in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: e6109a87750e588b12bfc9836c5db3db55420ec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133793"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Immagini Red Hat Enterprise Linux (RHEL) disponibili in Azure
Azure offre un'ampia gamma di immagini RHEL per diversi casi d'uso.

> [!NOTE]
> Tutte le immagini RHEL sono disponibili nei cloud pubblici di Azure e nei cloud di Azure per enti pubblici. Non sono disponibili nelle nuvole di Azure China.They are not available in Azure China clouds.

## <a name="list-of-rhel-images"></a>Lista di immagini RHEL
Questo è un elenco di immagini RHEL disponibili in Azure.This is a list of RHEL images available in Azure. Se non diversamente specificato, tutte le immagini sono partizionate con LVM e collegate ai normali repository RHEL (non EUS, non E4S). Le seguenti immagini sono attualmente disponibili per l'uso generale:

Offerta| SKU | Partizionamento | Provisioning | Note
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Agente Linux |
|             | 6.8      | RAW    | Agente Linux |
|             | 6.9      | RAW    | Agente Linux |
|             | 6.10     | RAW    | Agente Linux |
|             | 7-RAW    | RAW    | Agente Linux | Famiglia di immagini RHEL 7.x. <br> Attaccato a repository regolari per impostazione predefinita (non EUS).
|             | 7-LVM    | LVM    | Agente Linux | Famiglia di immagini RHEL 7.x. <br> Attaccato a repository regolari per impostazione predefinita (non EUS). Se stai cercando un'immagine RHEL standard da distribuire, usa questo set di immagini e/o la sua controparte di generazione 2.
|             | 7lvm-gen2| LVM    | Agente Linux | Generazione 2, famiglia di immagini RHEL 7.x. <br> Attaccato a repository regolari per impostazione predefinita (non EUS). Se stai cercando un'immagine RHEL standard da distribuire, usa questo set di immagini e/o la sua controparte di generazione 1.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | Famiglia di immagini RHEL 7.x. <br> Attaccato a repository regolari per impostazione predefinita (non EUS).
|             | 7.2      | RAW    | Agente Linux |
|             | 7.3      | RAW    | Agente Linux |
|             | 7.4      | RAW    | Agente Linux | A partire da aprile 2019, associati ai repository EUS.
|             | 74-gen2  | RAW    | Agente Linux | Per impostazione predefinita, associati ai repository EUS.
|             | 7.5      | RAW    | Agente Linux | A partire da giugno 2019, associati ai repository EUS.
|             | 75-gen2  | RAW    | Agente Linux | Per impostazione predefinita, associati ai repository EUS.
|             | 7.6      | RAW    | Agente Linux | A partire da maggio 2019, associati ai repository EUS.
|             | 76-gen2  | RAW    | Agente Linux | Per impostazione predefinita, associati ai repository EUS.
|             | 7.7      | LVM    | Agente Linux | Per impostazione predefinita, associati ai repository EUS.
|             | 8        | LVM    | Agente Linux | Famiglia di immagini RHEL 8.x
|             | 8-gen2   | LVM    | Agente Linux | Hyper-V Generazione 2 - Famiglia di immagini RHEL 8.x.
RHEL-SAP      | 7.4      | LVM    | Agente Linux | RHEL 7.4 per SAP HANA e Business Apps. Attaccato ai repository E4S, addebiterà un premio per SAP e RHEL, nonché la tassa di calcolo di base.
|             | 74sap-gen2| LVM    | Agente Linux | RHEL 7.4 per SAP HANA e Business Apps. Immagine della generazione 2. Attaccato ai repository E4S, addebiterà un premio per SAP e RHEL, nonché la tassa di calcolo di base.
|             | 7.5       | LVM    | Agente Linux | RHEL 7.5 per SAP HANA e Business Apps. Attaccato ai repository E4S, addebiterà un premio per SAP e RHEL, nonché la tassa di calcolo di base.
|             | 75sap-gen2| LVM    | Agente Linux | RHEL 7.5 per SAP HANA e Business Apps. Immagine della generazione 2. Attaccato ai repository E4S, addebiterà un premio per SAP e RHEL, nonché la tassa di calcolo di base.
|             | 7.6       | LVM    | Agente Linux | RHEL 7.6 per SAP HANA e Business Apps. Attaccato ai repository E4S, addebiterà un premio per SAP e RHEL, nonché la tassa di calcolo di base.
|             | 76sap-gen2| LVM    | Agente Linux | RHEL 7.6 per SAP HANA e Business Apps. Immagine della generazione 2. Attaccato ai repository E4S, addebiterà un premio per SAP e RHEL, nonché la tassa di calcolo di base.
|             | 7.7       | LVM    | Agente Linux | RHEL 7.7 per SAP HANA e Business Apps. Attaccato ai repository E4S, addebiterà un premio per SAP e RHEL, nonché la tassa di calcolo di base.
RHEL-SAP-HANA | 6.7       | RAW    | Agente Linux | RHEL 6.7 per SAP HANA. Non aggiornate a favore delle immagini RHEL-SAP.
|             | 7.2       | LVM    | Agente Linux | RHEL 7.2 per SAP HANA. Non aggiornate a favore delle immagini RHEL-SAP.
|             | 7.3       | LVM    | Agente Linux | RHEL 7.3 per SAP HANA. Non aggiornate a favore delle immagini RHEL-SAP.
RHEL-SAP-APPS | 6.8       | RAW    | Agente Linux | RHEL 6.8 per applicazioni aziendali SAP. Non aggiornate a favore delle immagini RHEL-SAP.
|             | 7.3       | LVM    | Agente Linux | RHEL 7.3 per applicazioni aziendali SAP. Non aggiornate a favore delle immagini RHEL-SAP.
RHEL-HA       | 7.4       | LVM    | Agente Linux | RHEL 7.4 con componente aggiuntivo HA. Addebiterà un premio per HA e RHEL in cima alla tassa di calcolo di base.
|             | 7.5       | LVM    | Agente Linux | RHEL 7.5 con componente aggiuntivo HA. Addebiterà un premio per HA e RHEL in cima alla tassa di calcolo di base.
|             | 7.6       | LVM    | Agente Linux | RHEL 7.6 con componente aggiuntivo HA. Addebiterà un premio per HA e RHEL in cima alla tassa di calcolo di base.
RHEL-SAP-HA   | 7.4          | LVM    | Agente Linux | RHEL 7.4 per SAP con HA e Update Services. Collegato ai repository E4S. Addebiterà un premio per i repository SAP e HA e RHEL, oltre alle commissioni di elaborazione di base.
|             | 74sapha-gen2 | LVM    | Agente Linux | RHEL 7.4 per SAP con HA e Update Services. Immagine della generazione 2. Collegato ai repository E4S. Addebiterà un premio per i repository SAP e HA e RHEL, oltre alle commissioni di elaborazione di base.
|             | 7.5          | LVM    | Agente Linux | RHEL 7.5 per SAP con HA e Update Services. Collegato ai repository E4S. Addebiterà un premio per i repository SAP e HA e RHEL, oltre alle commissioni di elaborazione di base.
|             | 7.6          | LVM    | Agente Linux | RHEL 7.6 per SAP con HA e Update Services. Collegato ai repository E4S. Addebiterà un premio per i repository SAP e HA e RHEL, oltre alle commissioni di elaborazione di base.
|             | 76sapha-gen2 | LVM    | Agente Linux | RHEL 7.6 per SAP con HA e Update Services. Immagine della generazione 2. Collegato ai repository E4S. Addebiterà un premio per i repository SAP e HA e RHEL, oltre alle commissioni di elaborazione di base.
|             | 7.7          | LVM    | Agente Linux | RHEL 7.7 per SAP con HA e Update Services. Collegato ai repository E4S. Addebiterà un premio per i repository SAP e HA e RHEL, oltre alle commissioni di elaborazione di base.
|             | 77sapha-gen2 | LVM    | Agente Linux | RHEL 7.7 per SAP con HA e Update Services. Immagine della generazione 2. Collegato ai repository E4S. Addebiterà un premio per i repository SAP e HA e RHEL, oltre alle commissioni di elaborazione di base.
rel-byos     |rhel-lvm74| LVM    | Agente Linux | Le immagini RHEL 7.4 BYOS, non allegate ad alcuna fonte di aggiornamenti, non addebiteranno un premio RHEL.
|             |rhel-lvm75| LVM    | Agente Linux | Le immagini RHEL 7.5 BYOS, non allegate ad alcuna fonte di aggiornamenti, non addebiteranno un premio RHEL.
|             |rhel-lvm76| LVM    | Agente Linux | Le immagini RHEL 7.6 BYOS, non allegate ad alcuna fonte di aggiornamenti, non addebiteranno un premio RHEL.
|             |rhel-lvm77| LVM    | Agente Linux | Le immagini RHEL 7.7 BYOS, non allegate ad alcuna fonte di aggiornamenti, non addebiteranno un premio RHEL.
|             |rhel-lvm8 | LVM    | Agente Linux | Le immagini RHEL 8 BYOS (la versione secondaria di RHEL è mostrata nel valore della versione dell'immagine), non allegate ad alcuna fonte di aggiornamenti, non addebiteranno un premio RHEL.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [immagini Red Hat in Azure](./redhat-images.md).
* Ulteriori informazioni [sull'infrastruttura Red Hat Update](./redhat-rhui.md).
* Ulteriori informazioni [sull'offerta RHEL BYOS](./byos.md).
* Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle (Ciclo di vita di Red Hat Enterprise Linux)](https://access.redhat.com/support/policy/updates/errata).
