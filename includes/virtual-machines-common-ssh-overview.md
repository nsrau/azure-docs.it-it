---
title: File di inclusione
description: File di inclusione
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 919582745d166cf8b3f3937f9bac4fc0dc1fe64f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2018
---
## <a name="overview-of-ssh-and-keys"></a>Panoramica di SSH e delle chiavi

SSH è un protocollo per connessioni crittografate che consente accessi protetti su connessioni non sicure. È il protocollo di connessione predefinito per le VM Linux ospitate in Azure. Sebbene SSH stessa fornisca una connessione crittografata, se si usano password con le connessioni SSH la VM rimane vulnerabile agli attacchi di forza bruta o di individuazione password. Un metodo più sicuro e preferito per la connessione a una VM mediante SSH è tramite una coppia di chiavi pubblica e privata, dette anche chiavi SSH. 

* La *chiave pubblica* si trova nella VM Linux o in qualsiasi altro servizio che si desidera usare con la crittografia a chiave pubblica.

* La *chiave privata* è quella che si presenta alla VM Linux quando ci si connette a SSH, per verificare la propria identità. Sulla chiave privata è necessario mantenere la massima riservatezza, evitando di condividerla.

A seconda dei criteri di sicurezza dell'organizzazione, è possibile riutilizzare una singola coppia di chiavi pubblica e privata per accedere a più VM e servizi di Azure. Non è necessaria una coppia di chiavi separata per ogni VM o servizio a cui si desidera accedere. 

La chiave pubblica può essere condivisa con chiunque, ma la chiave privata appartiene solo all'utente o all'infrastruttura di sicurezza locale.