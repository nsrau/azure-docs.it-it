---
title: File di inclusione
description: File di inclusione
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168611"
---
## <a name="overview-of-ssh-and-keys"></a>Panoramica di SSH e delle chiavi

[SSH](https://www.ssh.com/ssh/) è un protocollo per connessioni crittografate che consente accessi protetti su connessioni non sicure. SSH è il protocollo di connessione predefinito per le macchine virtuali Linux ospitate in Azure. Sebbene SSH stessa fornisca una connessione crittografata, se si usano password con le connessioni SSH la VM rimane vulnerabile agli attacchi di forza bruta o di individuazione password. Un metodo più sicuro e preferibile per la connessione a una macchina virtuale tramite SSH consiste nell'usare una coppia di chiavi pubblica e privata, dette anche *chiavi SSH*. 

* La *chiave pubblica* si trova nella VM Linux o in qualsiasi altro servizio che si desidera usare con la crittografia a chiave pubblica.

* La *chiave privata* rimane nel sistema locale. Sulla chiave privata è necessario mantenere la massima riservatezza, evitando di condividerla.

Quando si usa un client SSH per connettersi alla macchina virtuale Linux (che ha la chiave pubblica), la macchina virtuale remota testa il client per assicurarsi che disponga della chiave privata. In caso positivo, al client viene concesso il diritto di accesso alla macchina virtuale. 

A seconda dei criteri di sicurezza dell'organizzazione, è possibile riutilizzare una singola coppia di chiavi pubblica e privata per accedere a più VM e servizi di Azure. Non è necessaria una coppia di chiavi separata per ogni VM o servizio a cui si desidera accedere. 

La chiave pubblica può essere condivisa con chiunque, ma la chiave privata appartiene solo all'utente o all'infrastruttura di sicurezza locale.