---
title: Protezione avanzata negli host di macchine virtuali AKSSecurity hardening in AKS virtual machine hosts
description: Informazioni sulla protezione avanzata nel sistema operativo a controller di servizio per la macchina virtuale AKSLearn about the security hardening in AKS VM OS
services: container-service
author: mlearned
ms.topic: article
ms.date: 09/11/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: b7552fc083c5ed340dc54c2a31160b0c8b4bd076
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420908"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>Protezione avanzata per il sistema operativo host nodo agente AKSSecurity hardening for AKS agent node host OS

Il servizio Azure Kubernetes (AKS) è un servizio sicuro compatibile con gli standard SOC, ISO, PCI DSS e HIPAA. Questo articolo illustra la protezione avanzata applicata agli host di macchine virtuali AKS. Per altre informazioni sulla sicurezza di AKS, vedere Concetti relativi alla [sicurezza per applicazioni e cluster nel servizio Kubernetes di Azure (AKS)](https://docs.microsoft.com/azure/aks/concepts-security).

> [!Note]
> Questo documento ha come ambito gli agenti Linux solo in AKS.

I cluster AKS vengono distribuiti nelle macchine virtuali host, che eseguono un sistema operativo ottimizzato per la sicurezza che viene utilizzato per i contenitori in esecuzione su AKS. Questo sistema operativo host si basa su un'immagine **Ubuntu 16.04.LTS** con ulteriori operazioni di protezione e ottimizzazioni applicate (vedere Dettagli sulla protezione avanzata).

L'obiettivo del sistema operativo host con protezione con protezione con protezione con protezione con protezione sicura è ridurre l'area di attacco in superficie e ottimizzare per la distribuzione di contenitori in modo sicuro.

> [!Important]
> Il sistema operativo con protezione indurita NON è sottoposto a benchmarking CIS. Sebbene esistano sovrapposizioni con i benchmark CIS, l'obiettivo non è conforme alla CIS. L'obiettivo per la protezione avanzata del sistema operativo host consiste nel convergere a un livello di sicurezza coerente con gli standard di sicurezza dell'host interno di Microsoft.

## <a name="security-hardening-features"></a>Funzionalità di protezione avanzata

* AKS fornisce un sistema operativo host ottimizzato per la sicurezza per impostazione predefinita. Non è possibile selezionare un sistema operativo alternativo.

* Azure applica patch giornaliere (incluse le patch di sicurezza) agli host di macchine virtuali AKS. Alcune di queste patch richiederanno un riavvio, mentre altre no. L'utente è responsabile della pianificazione dei riavvii dell'host vm AKS in base alle esigenze. Per istruzioni su come automatizzare l'applicazione di patch AKS, vedere [Applicazione di patch ai nodi AKS.](https://docs.microsoft.com/azure/aks/node-updates-kured)

## <a name="what-is-configured"></a>Elementi configurati

| Cis  | Descrizione dell'audit|
|---|---|
| 1.1.1.1 |Assicurarsi che il montaggio dei file system cramfs sia disattivato|
| 1.1.1.2 |Assicurarsi che il montaggio dei file system freevxfs sia disabilitato|
| 1.1.1.3 |Assicurarsi che il montaggio dei file system jffs2 sia disabilitato|
| 1.1.1.4 |Assicurarsi che il montaggio dei file system HFS sia disabilitatoEnsure ing of HFS filesystems is disabled|
| 1.1.1.5 |Assicurarsi che il montaggio dei filesystem HFS Plus sia disabilitato|
|1.4.3 |Verificare l'autenticazione necessaria per la modalità utente singoloEnsure authentication required for single user mode |
|1.7.1.2 |Verificare che il banner di avviso di accesso locale sia configurato correttamenteEnsure local login warning banner is configured properly |
|1.7.1.3 |Verificare che il banner di avviso di accesso remoto sia configurato correttamenteEnsure remote login warning banner is configured properly |
|1.7.1.5 |Verificare che le autorizzazioni per /etc/problema siano configurateEnsure permissions on /etc/issue are configured |
|1.7.1.6 |Verificare che le autorizzazioni per /etc/issue.net siano configurateEnsure permissions on /etc/issue.net are configured |
|2.1.5 |Assicurarsi che --streaming-connection-idle-timeout non sia impostato su 0 |
|3.1.2 |Verificare che l'invio del reindirizzamento dei pacchetti sia disabilitatoEnsure packet redirect sending is disabled |
|3.2.1 |Assicurarsi che i pacchetti instradati di origine non siano accettatiEnsure source routed packages are not accepted |
|3.2.2 |Assicurarsi che i reindirizzamenti ICMP non siano accettati |
|3.2.3 |Assicurarsi che i reindirizzamenti ICMP sicuri non siano accettatiEnsure secure ICMP redirects are not accepted |
|3.2.4 |Assicurarsi che i pacchetti sospetti vengano registratiEnsure suspicious packets are logged |
|3.3.1 |Verificare che gli annunci router IPv6 non siano accettati |
|3.5.1 |Verificare che DCCP sia disabilitato |
|3.5.2 |Assicurarsi che SCTP sia disabilitatoEnsure SCTP is disabled |
|3.5.3 |Verificare che RDS sia disabilitatoEnsure RDS is disabled |
|3.5.4 |Assicurarsi che TIPC sia disabilitato |
|4.2.1.2 |Verificare che la registrazione sia configurataEnsure logging is configured |
|5.1.2 |Verificare che siano configurate le autorizzazioni per /etc/crontabEnsure permissions on /etc/crontab are configured |
|5.2.4 |Verificare che l'inoltro SSH X11 sia disabilitato |
|5.2.5 |Verificare che SSH MaxAuthTries sia impostato su 4 o meno |
|5.2.8 |Verificare che l'accesso root SSH sia disabilitatoEnsure SSH root login is disabled |
|5.2.10 |Verificare che SSH PermitUserEnvironment sia disabilitatoEnsure SSH PermitUserEnvironment is disabled |
|5.2.11 |Assicurarsi che vengano utilizzati solo gli algoritmi MAX approvati |
|5.2.12 |Verificare che l'intervallo di timeout di inattività SSH sia configuratoEnsure SSH Idle Timeout Interval is configured |
|5.2.13 |Assicurarsi che SSH LoginGraceTime sia impostato su un minuto o meno |
|5.2.15 |Verificare che il banner di avviso SSH sia configuratoEnsure SSH warning banner is configured |
|5.3.1 |Verificare che i requisiti di creazione delle password siano configuratiEnsure password creation requirements are configured |
|5.4.1.1 |Verificare che la scadenza della password sia pari o inferiore a 90 giorniEnsure password expiration is 90 days or less |
|5.4.1.4 |Verificare che il blocco della password inattivo sia pari o inferiore a 30 giorni |
|5.4.4 |Verificare che l'umask dell'utente predefinito sia 027 o più restrittivoEnsure default user umask is 027 or more restrictive |
|5.6 |Assicurarsi che l'accesso al comando su sia limitatoEnsure access to the su command is restricted|

## <a name="additional-notes"></a>Note aggiuntive
 
* Per ridurre ulteriormente la superficie di attacco, alcuni driver di modulo kernel non necessari sono stati disabilitati nel sistema operativo.

* Il sistema operativo con protezione indurita viene creato e mantenuto specificamente per AKS e NON è supportato al di fuori della piattaforma AKS.

## <a name="next-steps"></a>Passaggi successivi  

Per altre informazioni sulla sicurezza di AKS, vedere gli articoli seguenti:See the following articles for more information about AKS security: 

[Servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Considerazioni sulla sicurezza di AKSAKS security considerations](https://docs.microsoft.com/azure/aks/concepts-security)

[Procedure consigliate di AKS](https://docs.microsoft.com/azure/aks/best-practices)
