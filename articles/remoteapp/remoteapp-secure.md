---
title: Proteggere applicazioni e risorse in Azure RemoteApp | Documentazione Microsoft
description: Informazioni su come bloccare applicazioni e risorse in Azure RemoteApp
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 7fbade87-a453-426d-bfa5-c72227ea83cd
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: fb524efc41b1eaa36319726b3b8b534857e2afc7


---
# <a name="secure-apps-and-resources-in-azure-remoteapp"></a>Applicazioni protette e risorse in Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

RemoteApp di Azure fornisce agli utenti l'accesso alle applicazioni Windows gestite centralmente, il che consente di controllare ciò che gli utenti possono e non possono fare.  Ciò è particolarmente utile quando l'utente si connette da un dispositivo non gestito (ad esempio, i Macbook personali) e si desidera controllare l'accesso dell’utente o l'esperienza.

Ad esempio, se si utilizza Active Directory per l'autenticazione utente e si desidera impedire agli utenti di copiare dati da un'applicazione, è possibile configurare un criterio di gruppo Desktop remoto per impedire agli utenti la copia di dati.

Un altro esempio è se si desidera bloccare l'accesso a internet per una particolare applicazione nella raccolta. È possibile creare una regola Windows Firewall che blocca l'accesso quando si crea l'immagine per la raccolta.

## <a name="implementation-options"></a>Opzioni di implementazione
  Ecco le opzioni di implementazione della chiave, che possono essere utilizzate singolarmente o combinate, se necessario:

1. Se la raccolta di RemoteApp fa parte del dominio è possibile applicare [Criteri di gruppo](https://technet.microsoft.com/library/cc725828.aspx) qualsiasi (fatta eccezione per i criteri di timeout di disconnessione e inattivo e descritti[qui](../azure-subscription-service-limits.md)).
2. In alternativa a criteri di gruppo (se la raccolta non fa parte del dominio o non si dispone di privilegi appropriati in Active Directory), è possibile configurare [criteri locali](https://technet.microsoft.com/library/cc775702.aspx) nell'immagine modello.  Si noti che Criteri di gruppo si sovrascrivono ai criteri locali quando si verifica un conflitto.
3. Alcune impostazioni del sistema operativo/app non sono configurabili tramite criteri, ma è possibile tramite la chiave del Registro di sistema utilizzando lo [strumento RegEdit](remoteapp-hybridtrouble.md) durante la configurazione dell'immagine modello.
4. È possibile utilizzare [Windows Firewall](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish) per controllare l'accesso di rete da e verso il computer in cui è in esecuzione l'applicazione. Assicurarsi semplicemente di non bloccare gli URL e le porte definite in questa sezione.
5. È possibile utilizzare [AppLocker](https://technet.microsoft.com/library/hh831440.aspx) per controllare quali applicazioni e quali file gli utenti possono eseguire. Ad esempio, gli utenti esperti possono scoprire come eseguire applicazioni che non sono state pubblicate, ma che sono disponibili nell'immagine utilizzata per creare la raccolta, AppLocker può bloccare tutto questo.

## <a name="detailed-information"></a>Informazioni dettagliate
* Probabilmente i seguenti criteri di servizi desktop sono più utili :
  * [Reindirizzamento di Dispositivi e risorse](https://technet.microsoft.com/library/ee791794.aspx)
  * [Reindirizzamento della stampante](https://technet.microsoft.com/library/ee791784.aspx)
  * [Profili](https://technet.microsoft.com/library/ee791865.aspx).
* Si noti che la configurazione dei reindirizzamenti tramite il modulo RemoteApp PowerShell (come visto [qui](remoteapp-redirection.md)) si basa sul computer client per applicare il criterio, in modo che se l'obiettivo principale è la protezione è opportuno applicare i criteri tramite criteri locali immagine modello o tramite criteri di gruppo.
* [Criteri di Windows Server 2012 R2](https://technet.microsoft.com/library/hh831791.aspx).
* [Criteri di office 2013](https://technet.microsoft.com/library/cc178969.aspx) (tra cui [come personalizzare la barra degli strumenti di Office](https://technet.microsoft.com/library/cc179143.aspx)).




<!--HONumber=Dec16_HO2-->


