---
title: Risolvere i problemi relativi ai volumi a doppio protocollo per Azure NetApp Files | Microsoft Docs
description: Vengono descritti i messaggi di errore e le risoluzioni che consentono di risolvere i problemi relativi a doppio protocollo per Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/01/2020
ms.author: b-juche
ms.openlocfilehash: cc046a27fec1b9e361ff840c7ae0f077e2987b67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654207"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>Risolvere i problemi relativi ai volumi con doppio protocollo

Questo articolo descrive le risoluzioni alle condizioni di errore che possono essere presenti durante la creazione o la gestione di volumi a doppio protocollo.

## <a name="error-conditions-and-resolutions"></a>Condizioni di errore e risoluzioni

|     Condizioni di errore    |     Soluzione    |
|-|-|
| La creazione del volume a doppio protocollo ha esito negativo con l'errore `This Active Directory has no Server root CA Certificate` .    |     Se questo errore si verifica quando si crea un volume a doppio protocollo, verificare che il certificato CA radice sia caricato nell'account NetApp.    |
| La creazione del volume a doppio protocollo ha esito negativo con l'errore `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  Prendere in considerazione le risoluzioni seguenti:   <ul><li>Verificare che il certificato radice richiesto venga aggiunto quando si aggiunge Active Directory (AD) all'account NetApp. Vedere [caricare Active Directory certificato radice pubblico dell'autorità di certificazione](create-volumes-dual-protocol.md#upload-active-directory-certificate-authority-public-root-certificate).   </li><li>Il record del puntatore (PTR) del computer host Active Directory potrebbe non essere presente nel server DNS. È necessario creare una zona di ricerca inversa nel server DNS, quindi aggiungere un record PTR del computer host AD nella zona di ricerca inversa. <br> Si supponga, ad esempio, che l'indirizzo IP del computer AD sia `1.1.1.1` , il nome host del computer ad (come rilevato tramite il `hostname` comando) sia `AD1` e il nome di dominio sia `myDomain.com` .  Il record PTR aggiunto alla zona di ricerca inversa deve essere `1.1.1.1`  ->  `AD1.myDomain.com` . </li></ul>  |
| La creazione del volume a doppio protocollo ha esito negativo con l'errore `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` . |  Questo errore indica che la password di Active Directory non è corretta quando Active Directory viene aggiunto all'account NetApp. Aggiornare la connessione AD con la password corretta e riprovare. |
| La creazione del volume a doppio protocollo ha esito negativo con l'errore `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Questo errore indica che il DNS non è raggiungibile. Il motivo potrebbe essere dovuto al fatto che l'indirizzo IP DNS non è corretto o si è verificato un problema di rete. Controllare l'indirizzo IP DNS immesso nella connessione AD e verificare che l'indirizzo IP sia corretto. <br> Assicurarsi inoltre che l'annuncio e il volume si trovino nella stessa area e nella stessa VNet. Se si trovano in reti virtuali diversi, assicurarsi che il peering VNet venga stabilito tra i due reti virtuali.|
| Errore di autorizzazione negata durante il montaggio di un volume a doppio protocollo. | Un volume con doppio protocollo supporta i protocolli NFS e SMB.  Quando si tenta di accedere al volume montato sul sistema UNIX, il sistema tenta di eseguire il mapping dell'utente UNIX usato a un utente di Windows. Se non viene trovato alcun mapping, si verifica l'errore "autorizzazione negata". <br> Questa situazione si verifica anche quando si usa l'utente "root" per l'accesso. <br> Per evitare il problema "autorizzazione negata", assicurarsi che Windows Active Directory includa `pcuser` prima di accedere al punto di montaggio. Se si aggiunge `pcuser` dopo aver rilevato il problema "autorizzazione negata", attendere 24 ore prima che la voce della cache venga cancellata prima di riprovare ad accedere. |

## <a name="next-steps"></a>Passaggi successivi  

* [Creare un volume con doppio protocollo](create-volumes-dual-protocol.md)
* [Configurare un client NFS per Azure NetApp Files](configure-nfs-clients.md)
