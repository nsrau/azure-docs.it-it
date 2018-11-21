---
title: File di inclusione
description: File di inclusione
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: cff3d7bfb89d5b03f986da32edc148efcfb7e7bd
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2018
ms.locfileid: "51506340"
---
## <a name="overview-of-ssh-and-keys"></a>Panoramica di SSH e delle chiavi

SSH è un protocollo per connessioni crittografate che consente accessi protetti su connessioni non sicure. SSH è il protocollo di connessione predefinito per le macchine virtuali Linux ospitate in Azure. Sebbene SSH stessa fornisca una connessione crittografata, se si usano password con le connessioni SSH la VM rimane vulnerabile agli attacchi di forza bruta o di individuazione password. Un metodo più sicuro e preferibile per la connessione a una macchina virtuale tramite SSH consiste nell'usare una coppia di chiavi pubblica e privata, dette anche *chiavi SSH*. 

* La *chiave pubblica* si trova nella VM Linux o in qualsiasi altro servizio che si desidera usare con la crittografia a chiave pubblica.

* La *chiave privata* nel sistema locale viene usata da un client SSH per verificare l'identità quando ci si connette alla macchina virtuale Linux. Sulla chiave privata è necessario mantenere la massima riservatezza, evitando di condividerla.

A seconda dei criteri di sicurezza dell'organizzazione, è possibile riutilizzare una singola coppia di chiavi pubblica e privata per accedere a più VM e servizi di Azure. Non è necessaria una coppia di chiavi separata per ogni VM o servizio a cui si desidera accedere. 

La chiave pubblica può essere condivisa con chiunque, ma la chiave privata appartiene solo all'utente o all'infrastruttura di sicurezza locale.