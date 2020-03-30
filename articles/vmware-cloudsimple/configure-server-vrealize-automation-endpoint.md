---
title: Azure VMware Solution by CloudSimple - Set up vCenter on Private Cloud for vRealize Automation
description: Descrive come configurare un server VMware vCenter nel cloudSimple Private Cloud come endpoint per l'automazione vRealize VMware
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024841"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>Configurare vCenter sul cloud privato per VMware vRealize Automation

È possibile configurare un server VMware vCenter nel cloud CloudSimple Private Cloud come endpoint per l'automazione vReali vReali di VMware.

## <a name="before-you-begin"></a>Prima di iniziare

Completare queste attività prima di configurare il server vCenter:

* Configurare una connessione VPN da [sito a sito](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tra l'ambiente locale e il cloud privato.
* [Configurare l'inoltro DNS delle richieste DNS locali ai](on-premises-dns-setup.md) server DNS per il cloud privato.
* Inviare una richiesta di [supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) per creare un utente amministrativo vRealize Automation IaaS con il set di autorizzazioni elencate nella tabella seguente.

| Valore dell'attributo | Autorizzazione |
------------ | ------------- |  
| Archivio dati |  Alloca spazio <br> Sfoglia Datastore |
| Cluster di archivi di dati | Configurare un cluster di archivi datiConfigure a Datastore Cluster |
| Cartella | Creazione cartella <br>Elimina cartella |
| Global |  Gestione degli attributi personalizzati<br>Imposta attributo personalizzato |
| Rete | Assegna rete |
| Autorizzazioni | Modify Permissions |
| Risorsa | Assegnare la macchina virtuale al pool di risorseAssign VM to Resource Pool<br>Eseguire la migrazione della macchina virtuale spenta<br>Eseguire la migrazione della macchina virtuale alimentata |
| Inventario macchine virtuali |  Crea da esistente<br>Creazione di un nuovo sito<br>Spostamento<br>Rimuovere | 
| Interazione macchina virtuale |  Configurare il supporto CD<br>Interazione con la console<br>Connessione del dispositivo<br>Spegnimento<br>Accensione<br>Reset<br>Sospendi<br>Installazione degli strumenti | 
| Configurazione della macchina virtualeVirtual Machine Configuration |  Aggiungi disco esistente<br>Aggiungi nuovo disco<br>Aggiungi o Rimuovi<br>Rimuovi disco<br>Avanzate<br>Modifica conteggio CPU<br>Cambia risorsa<br>Estendi disco virtuale<br>Rilevamento delle modifiche del disco<br>Memoria<br>Modifica delle impostazioni del dispositivo<br>Rinominare<br>Imposta annotazione (versione 5.0 e successive)<br>Impostazioni<br>Posizionamento del file di swap |
| Provisioning |  Personalizza<br>Clona modello<br>Clona macchina virtuale<br>Modello di distribuzione<br>Leggi le specifiche di personalizzazione |
| Stato macchina virtuale | Crea istantanea<br>Rimuovi istantanea<br>Ripristina l'istantanea |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Installare vRealize Automation nell'ambiente localeInstall vRealize Automation in your on-premises environment

1. Accedi all'appliance server vRealize Automation IaaS come amministratore iaaS creato automaticamente da CloudSimple Support.
2. Distribuire un agente vSphere per l'endpoint di automazione vReali.
    1. Passare a https://*vra-url*:5480/installer, dove *vra-url* è l'URL utilizzato per accedere all'interfaccia utente di amministrazione di vRealize Automation.
    2. Fare clic sul **programma di installazione di IaaS** per scaricare il programma di installazione.<br>
    La convenzione di denominazione per il file del programma di installazione è setup_*vra-url*@5480.exe.
    3. Eseguire il programma di installazione. Nella schermata iniziale fare clic su **Avanti**.
    4. Accettare l'EULA e fare clic su **Avanti**.
    5. Fornire le informazioni di accesso, fare clic su **Accetta certificato**e quindi su **Avanti**.
    ![Credenziali vRA](media/configure-vra-endpoint-login.png)
    6. Selezionare Agenti **proxy** e **di installazione personalizzati** e fare clic su **Avanti**.
    ![Tipo di installazione vRA](media/configure-vra-endpoint-install-type.png)
    7. Immettere le informazioni di accesso del server IaaS e fare clic su **Avanti**. Se si utilizza Active Directory, immettere il nome utente nel formato **dominio/utente.** In caso **user@domain** contrario, utilizzare format.
    ![informazioni di accesso vRA](media/configure-vra-endpoint-account.png)
    8. Per le impostazioni proxy, immettere **vSphere** per **Tipo di agente**. Immettere un nome per l'agente.
    9. Immettere l'FQDN del server IaaS nei campi **Host servizio** di gestione e Host servizio Web di **Model Manager.** Fare clic su **Test** per testare la connessione per ognuno dei valori FQDN. Se il test ha esito negativo, modificare le impostazioni DNS in modo che il nome host del server IaaS venga risolto.
    10. Immettere un nome per l'endpoint server vCenter per il cloud privato. Registrare il nome da utilizzare in un secondo momento nel processo di configurazione.

        ![Proxy di installazione vRA](media/configure-vra-endpoint-proxy.png)

    11. Fare clic su **Avanti**.
    12. Fare clic su **Installa**.

## <a name="configure-the-vsphere-agent"></a>Configurare l'agente vSphere

1. Passare a https://*vra-url*/vcac e accedere come **ConfigurationAdmin**.
2. Selezionare**Endpoint** >  **dell'infrastruttura** > **.**
3. Selezionare **Nuova** > **virtual** > **vSphere**.
4. Immettere il nome dell'endpoint vSphere specificato nella procedura precedente.
5. Per **Indirizzo**, immettere l'URL del server vCenter del cloud privato nel formato https://*vcenter-fqdn*/sdk, dove *vcenter-fqdn* è il nome del server vCenter.
6. Immettere le credenziali per l'utente amministrativo vRealize Automation IaaS creato automaticamente dal supporto CloudSimple.
7. Fare clic su **Test connessione** per convalidare le credenziali utente. Se il test ha esito negativo, verificare l'URL, le informazioni sull'account e il [nome dell'endpoint](#verify-the-endpoint-name) e riprovare.
8. Dopo un test riuscito, fare clic **su OK** per creare l'endpoint vSphere.
    ![Accesso alla configurazione dell'endpoint vRA](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Verificare il nome dell'endpointVerify the endpoint name

Per identificare il nome dell'endpoint server vCenter corretto, eseguire le operazioni seguenti:

1. Aprire un prompt dei comandi nell'appliance IaaS.
2. Modificare la directory in C:, file di programma (x86) VMware , VCAC , dove *nome-agente* è il nome assegnato all'endpoint server vCenter.
3. Eseguire il comando seguente.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

L'output è simile al seguente. Il valore `managementEndpointName` del campo è il nome dell'endpoint.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
