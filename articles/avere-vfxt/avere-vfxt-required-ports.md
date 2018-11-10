---
title: Elenco di porte consentite per Avere vFXT per Azure
description: Elenco delle porte usate da Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 394415ffc7593d058d8d0bf1c0040b88ec25073b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669928"
---
# <a name="required-ports"></a>Porte richieste

Le porte elencate in questa sezione vengono usate per le comunicazioni in ingresso e in uscita di vFXT.

Non esporre mai il cluster vFXT o l'istanza del controller del cluster direttamente a Internet pubblico.

## <a name="api"></a>API

| In ingresso: | | |
| --- | ---- | --- |
| TCP | 22  | SSH  |
| TCP | 443 | HTTPS|



| In uscita: |     |       |
|----------|-----|-------|
| TCP      | 443 | HTTPS |

 
## <a name="nfs"></a>NFS

| In ingresso e in uscita  | | |
| --- | --- | ---|
| TCP/UDP | 111  | RPCBIND  |
| TCP/UDP | 2049 | NFS      |
| TCP/UDP | 4045 | NLOCKMGR |
| TCP/UDP | 4046 | MOUNTD   |
| TCP/UDP | 4047 | STATO   |

