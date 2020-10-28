---
title: Segmenti di rete di VMware HCX
description: Per VMware HCX sono necessarie quattro reti.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 8137b4383d2a243d53db317db6f5a78b3bc68e67
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173621"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Per VMware HCX sono necessarie quattro reti:

- **Rete di gestione:** si tratta in genere della stessa rete di gestione usata nel cluster vSphere. Identificare almeno due indirizzi IP in questo segmento di rete per VMware HCX. Potrebbero esserne necessari di più a seconda della distribuzione.

- **Rete vMotion:** si tratta in genere della stessa rete usata per vMotion nel cluster vSphere.  Identificare almeno due indirizzi IP in questo segmento di rete per VMware HCX. Potrebbero esserne necessari di più a seconda della distribuzione.  

   La rete vMotion deve essere esporta in un commutatore virtuale distribuito o vSwitch0. In caso contrario, modificare l'ambiente.

   > [!NOTE]
   > Se questa rete non è indirizzata (privata), non si verifica alcun problema.

- **Rete uplink:** si vuole creare una nuova rete per VMware HCX Uplink ed estenderla nel cluster vSphere tramite un gruppo di porte. Identificare almeno due indirizzi IP in questo segmento di rete per VMware HCX. Potrebbero esserne necessari di più a seconda della distribuzione.  

   > [!NOTE]
   > Il metodo raccomandato consiste nel creare una rete /29, ma è possibile specificare qualsiasi dimensione di rete.

- **Rete di replica:** si vuole creare una nuova rete per VMware HCX Replication ed estendere tale rete nel cluster vSphere tramite un gruppo di porte. Identificare almeno due indirizzi IP in questo segmento di rete per VMware HCX. Potrebbero esserne necessari di più a seconda della distribuzione.

   > [!NOTE]
   > Il metodo raccomandato consiste nel creare una rete /29, ma è possibile specificare qualsiasi dimensione di rete.