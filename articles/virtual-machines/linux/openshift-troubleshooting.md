---
title: Risoluzione dei problemi relativi alla distribuzione di OpenShift in Azure | Microsoft Docs
description: Risoluzione dei problemi relativi alla distribuzione di OpenShift in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: ea3664870480f6ed170972a5f52940dc4a852219
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshooting-openshift-deployment-in-azure"></a>Risoluzione dei problemi relativi alla distribuzione di OpenShift in Azure

Se il cluster OpenShift non viene distribuito correttamente, è possibile eseguire alcune attività di risoluzione dei problemi per circoscrivere il problema. Visualizzare lo stato della distribuzione e confrontarlo con l'elenco di codici di uscita seguente.

- Codice di uscita 3: nome utente/password oppure ID organizzazione/chiave di attivazione di Red Hat non sono corretti
- Codice di uscita 4: l'ID del Pool di Red Hat non è corretto o non ci sono diritti disponibili
- Codice di uscita 5: non è possibile effettuare il provisioning del volume del thin pool Docker
- Codice di uscita 6: l'installazione del cluster OpenShift non è riuscita
- Codice di uscita 7: l'installazione del cluster OpenShift è riuscita, ma la configurazione del provider di servizi cloud Azure no. Problema di configurazione master sul nodo master
- Codice di uscita 8: l'installazione del cluster OpenShift è riuscita, ma la configurazione del provider di servizi cloud Azure no. Problema di configurazione del nodo sul nodo master
- Codice di uscita 9: l'installazione del cluster OpenShift è riuscita, ma la configurazione del provider di servizi cloud Azure no. Problema di configurazione del nodo sul nodo dell'infrastruttura o dell'app
- Codice di uscita 10: l'installazione del cluster OpenShift è riuscita, ma la configurazione del provider di servizi cloud Azure no. Correzione dei nodi master oppure impossibile impostare il nodo master come non pianificabile
- Codice di uscita 11: errore di distribuzione delle metriche
- Codice di uscita 12: errore di distribuzione dell'account di accesso

Per i codici di uscita 7-10, l'installazione del cluster OpenShift è stata eseguita, ma la configurazione del provider di servizi cloud Azure non è riuscita. È possibile connettersi tramite SSH al nodo master (Origin) o al nodo Bastion (Container Platform) e quindi da questa posizione connettersi tramite SSH a ognuno dei nodi del cluster e risolvere i problemi.

Una causa comune degli errori con codici di uscita 7-9 è che l'entità servizio non ha le autorizzazioni appropriate per la sottoscrizione o il gruppo di risorse. Se il problema è effettivamente questo, assegnare le autorizzazioni corrette ed eseguire di nuovo manualmente lo script che ha generato l'errore e tutti gli script successivi.
Assicurarsi di riavviare il servizio per il quale si è verificato l'errore (ad esempio, systemctl restart atomic-openshift-node.service) prima di eseguire nuovamente gli script.

Per altre operazioni di risoluzione dei problemi, connettersi tramite SSH al nodo master sulla porta 2200 (Origin) o al nodo Bastion sulla porta 22 (Container Platform). È necessario spostarsi nella radice (sudo su -) e quindi passare alla directory seguente: /var/lib/waagent/custom-script/download

Dovrebbero venire visualizzate due cartelle denominate "0" e "1". In ognuna di queste cartelle sono presenti due file, stderr e stdout. È possibile esaminare questi file per determinare dove si è verificato l'errore.
