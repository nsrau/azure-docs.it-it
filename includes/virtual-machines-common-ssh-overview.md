---
title: includere il file
description: includere file
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513188"
---
## <a name="overview-of-ssh-and-keys"></a>Panoramica di SSH e delle chiavi

[SSH](https://www.ssh.com/ssh/) è un protocollo di connessione crittografato che fornisce accessi sicuri su connessioni non protette. SSH è il protocollo di connessione predefinito per le macchine virtuali Linux ospitate in Azure. Sebbene SSH fornisca una connessione crittografata, l'uso delle password con le connessioni SSH lascia la VM vulnerabile agli attacchi di forza bruta. Si consiglia di connettersi a una macchina virtuale tramite SSH usando una coppia di chiavi pubblica/privata, nota anche come *chiavi SSH*. 

- La *chiave pubblica* viene posizionata nella VM Linux.

- La *chiave privata* rimane nel sistema locale. Sulla chiave privata è necessario mantenere la massima riservatezza, evitando di condividerla.

Quando si usa un client SSH per connettersi alla VM Linux (che ha la chiave pubblica), la macchina virtuale remota testa il client per assicurarsi che disponga della chiave privata corretta. In caso positivo, al client viene concesso il diritto di accesso alla macchina virtuale. 

A seconda dei criteri di sicurezza dell'organizzazione, è possibile riutilizzare una singola coppia di chiavi pubblica e privata per accedere a più VM e servizi di Azure. Non è necessaria una coppia di chiavi separata per ogni VM o servizio a cui si desidera accedere. 

La chiave pubblica può essere condivisa con altri utenti, ma solo l'utente o l'infrastruttura di sicurezza locale deve avere accesso alla chiave privata.