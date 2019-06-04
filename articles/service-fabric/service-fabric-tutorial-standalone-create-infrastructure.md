---
title: Esercitazione sulla creazione dell'infrastruttura per un cluster di Service Fabric in AWS - Azure Service Fabric | Microsoft Docs
description: In questa esercitazione è illustrato come configurare l'infrastruttura di AWS per l'esecuzione di un cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 69508628356a5f33073311e4d062d66875509192
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302470"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>Esercitazione: Creare l'infrastruttura AWS per ospitare un cluster di Service Fabric

I cluster autonomi di Service Fabric offrono la possibilità di scegliere il proprio ambiente e creare un cluster come parte dell'approccio "qualsiasi sistema operativo, qualsiasi cloud" adottato da Service Fabric. In questa serie di esercitazioni viene creato un cluster autonomo ospitato in AWS e viene installata un'applicazione al suo interno.

Questa è la prima di una serie di esercitazioni. In questo articolo vengono generate le risorse AWS necessarie per ospitare il cluster autonomo di Service Fabric. Nei prossimi articoli si installerà la suite autonoma di Service Fabric, si installerà un'applicazione di esempio nel cluster e, infine, si pulirà il cluster.

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un set di istanze EC2
> * Modificare il gruppo di sicurezza
> * Accedere a una delle istanze
> * Preparare l'istanza per Service Fabric

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario un account AWS.  Se non si dispone ancora di un account, accedere alla [Console AWS](https://aws.amazon.com/) per crearne uno.

## <a name="create-ec2-instances"></a>Creare istanze EC2

Accedere alla Console AWS, immettere **EC2** nella casella di ricerca e selezionare **EC2 Virtual Servers in the Cloud** (Server virtuali EC2 nel cloud).

![Ricerca nella Console AWS][aws-console]

Selezionare **Launch Instance** (Avvia istanza), quindi nella schermata successiva scegliere **Select** (Seleziona) accanto a Microsoft Windows Server 2016 Base.

![Selezione dell'istanza EC2][aws-ec2instance]

Selezionare **t2.medium** e quindi **Next: Configure Instance Details** (Avanti: Configura dettagli dell'istanza), nella schermata successiva modificare il numero di istanze in `3` e infine selezionare **Advanced Details** (Dettagli avanzati) per espandere la sezione.

Per connettere le macchine virtuali insieme in Service Fabric, le macchine virtuali che ospitano l'infrastruttura devono avere le stesse credenziali.  Esistono due modi comuni per ottenere credenziali coerenti: unirle tutte allo stesso dominio o impostare la stessa password di amministratore in ogni macchina virtuale.  Per questa esercitazione, viene usato uno script di dati utente per impostare le istanze EC2 in modo da avere tutte la stessa password.  In un ambiente di produzione è più sicuro unire gli host a un dominio Windows.

Immettere lo script seguente nel campo dei dati utente nella Console:

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

Dopo aver immesso lo script PowerShell selezionare **Review and Launch** (Rivedi e avvia)

![Revisione e avvio di EC2][aws-ec2configure2]

Nella schermata di revisione selezionare **Launch** (Avvia).  Selezionare **Proceed without a key pair** (Procedi senza coppia di chiavi) nell'elenco a discesa e selezionare la casella di controllo che indica che si conosce la password.

![Selezione della coppia di chiavi di AWS][aws-keypair]

Infine, selezionare **Launch Instances** (Avvia istanze) e quindi **View Instances** (Visualizza istanze).  La base per il cluster di Service Fabric è stata creata; a questo punto è necessario aggiungere alcune configurazioni finali alle istanze per prepararle alla configurazione di Service Fabric.

## <a name="modify-the-security-group"></a>Modificare il gruppo di sicurezza

Service Fabric richiede un determinato numero di porte aperte tra gli host nel cluster. Per aprire queste porte nell'infrastruttura AWS, selezionare una delle istanze create. Quindi selezionare il nome del gruppo di sicurezza, ad esempio **launch-wizard-1**. A questo punto, selezionare la scheda **Inbound** (In ingresso).

Per evitare di aprire queste porte a chiunque, è necessario aprirle solo agli host nello stesso gruppo di sicurezza. Prendere nota dell'ID del gruppo di sicurezza, nell'esempio **sg c4fb1eba**.  A questo punto selezionare **Edit** (Modifica).

Successivamente, aggiungere quattro regole al gruppo di sicurezza per le dipendenze dei servizi e quindi altre tre per Service Fabric stesso. La prima regola serve a consentire il traffico ICMP per i controlli della connettività di base. Le altre regole aprono le porte necessarie per abilitare SMB e Registro di sistema remoto.

Per la prima regola selezionare **Add Rule** (Aggiungi regola), quindi nel menu a discesa selezionare **All ICMP - IPv4** (Tutti ICMP - IPv4). Selezionare la casella di immissione accanto Custom (Personalizzato) e immettere l'ID del gruppo di sicurezza precedente riportato sopra.

Per le ultime tre dipendenze, è necessario seguire un processo simile.  Selezionare **Add Rule**, nell'elenco a discesa selezionare **Custom TCP Rule** (Regola TCP personalizzata) e nell'intervallo di porte immettere uno tra i valori `135`, `137-139` e `445` per ogni regola. Infine, nella casella di origine immettere l'ID del gruppo di sicurezza.

![Porte del gruppo di sicurezza][aws-ec2securityports]

Ora che le porte per le dipendenze sono aperte, è necessario eseguire la stessa operazione per le porte che Service Fabric stesso usa per comunicare. Selezionare **Add Rule**, nell'elenco a discesa selezionare **Custom TCP Rule** e nell'intervallo di porte immettere `20001-20031`, quindi immettere il gruppo di sicurezza nella casella Source (Origine).

Successivamente, aggiungere una regola per l'intervallo di porte temporanee.  Selezionare **Add Rule**, nell'elenco a discesa selezionare **Custom TCP Rule** e nell'intervallo di porte immettere `20606-20861`. Infine, nella casella di origine immettere l'ID del gruppo di sicurezza.

Aprire a chiunque le ultime due regole di Service Fabric per poter gestire il cluster di Service Fabric dal personal computer. Selezionare **Add Rule**, nell'elenco a discesa selezionare **Custom TCP Rule**, nell'intervallo di porte immettere uno tra i valori `19000-19003` e `19080-19081` e quindi selezionare Anywhere (Ovunque) per Source.

Infine, è necessario aprire la porta 8080 per vedere l'applicazione quando viene distribuita. Selezionare **Add Rule**, nell'elenco a discesa selezionare **Custom TCP Rule**, nell'intervallo di porte immettere `8080` e quindi selezionare Anywhere per Source.

Tutte le regole sono immesse. Selezionare **Salva**.

## <a name="connect-to-an-instance-and-validate-connectivity"></a>Connettersi a un'istanza e convalidare la connettività

Nella scheda del gruppo di sicurezza selezionare **Instances** (Istanze) dal menu a sinistra.  Selezionare ciascuna delle istanze create e notare che gli indirizzi IP privati per gli esempi seguenti useranno `172.31.21.141` e `172.31.20.163`.

Dopo che tutti gli indirizzi IP sono presenti, selezionare una delle istanze a cui connettersi, fare clic con il pulsante destro del mouse sull'istanza e selezionare **Connect** (Connetti).  Da qui è possibile scaricare il file RDP per questa istanza specifica.  Selezionare **Download Remote Desktop File** (Scarica file desktop remoto) e aprire il file che viene scaricato per stabilire la connessione al desktop remoto (RDP) a questa istanza.  Quando viene richiesto, immettere la password `serv1ceF@bricP@ssword`.

![Scaricare il file desktop remoto][aws-rdp]

Dopo aver stabilito correttamente la connessione con la propria istanza, convalidare che sia possibile connettersi e condividere i file.  Dopo che sono stati raccolti gli indirizzi IP per tutte le istanze, selezionarne uno a cui attualmente non si è connessi. Andare su **Start**, immettere `cmd` e selezionare **Commad Prompt** (Prompt dei comandi).

In questi esempi è stata stabilita la connessione RDP con l'indirizzo IP seguente: 172.31.21.141. Tutti i test di connettività vengono quindi eseguiti con l'altro indirizzo IP: 172.31.20.163.

Per convalidare che la connettività di base funzioni, usare il comando ping.

```
ping 172.31.20.163
```

Se l'output avrà un aspetto simile a `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` ripetuto quattro volte, significa che la connessione tra le istanze è funzionante.  A questo punto convalidare che la condivisione SMB funzioni con il comando seguente:

```
net use * \\172.31.20.163\c$
```

L'output restituito dovrebbe essere `Drive Z: is now connected to \\172.31.20.163\c$.`.

## <a name="prep-instances-for-service-fabric"></a>Preparare le istanze per Service Fabric

In caso di creazione da zero, è necessario eseguire alcuni passaggi aggiuntivi.  Ciò significa che è necessario convalidare che Registro di sistema remoto fosse in esecuzione, abilitare SMB e aprire le porte necessarie per SMB e Registro di sistema remoto.

Per semplificare, tutto questo lavoro è stato incorporato quando è stato eseguito il bootstrap delle istanze con lo script di dati dell'utente.

Per abilitare SMB è stato usato questo comando di PowerShell:

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

Questo è il comando di PowerShell per aprire le porte nel firewall:

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte della serie è stato descritto come avviare tre istanze EC2 e ottenere la configurazione per l'installazione di Service Fabric:

> [!div class="checklist"]
> * Creare un set di istanze EC2
> * Modificare il gruppo di sicurezza
> * Accedere a una delle istanze
> * Preparare l'istanza per Service Fabric

Passare alla seconda parte della serie per configurare Service Fabric nel cluster.

> [!div class="nextstepaction"]
> [Installare Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png