---
title: "Soluzioni VMware di Azure (AVS): configurare vCenter in un cloud privato AVS per l'automazione di vRealize"
description: Viene descritto come configurare un server VMware vCenter nel cloud privato AVS come endpoint per l'automazione vRealize VMware
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 41106498594ac05b944323e5f5e63de739aedf37
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024841"
---
# <a name="set-up-vcenter-on-your-avs-private-cloud-for-vmware-vrealize-automation"></a>Configurare vCenter nel cloud privato AVS per l'automazione di vRealize VMware

È possibile configurare un server VMware vCenter nel cloud privato AVS come endpoint per l'automazione vRealize di VMware.

## <a name="before-you-begin"></a>Prima di iniziare

Completare queste attività prima di configurare il server vCenter:

* Configurare una [connessione VPN da sito a sito](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tra l'ambiente locale e il cloud privato AVS.
* [Configurare l'invio DNS di richieste DNS locali](on-premises-dns-setup.md) ai server DNS per il cloud privato AVS.
* Inviare una [richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) per creare un utente amministratore IaaS di automazione vRealize con il set di autorizzazioni elencate nella tabella seguente.

| Valore dell'attributo | Autorizzazione |
------------ | ------------- |  
| Archivio dati |  Alloca spazio <br> Sfoglia archivio dati |
| Cluster di archivio dati | Configurare un cluster di archivio dati |
| Cartella | Creazione cartella <br>Elimina cartella |
| Globale |  Gestire attributi personalizzati<br>Imposta attributo personalizzato |
| Rete | Assegna rete |
| Autorizzazioni | Modificare le autorizzazioni |
| Gruppi | Assegnare una macchina virtuale al pool di risorse<br>Esegui la migrazione della macchina virtuale spenta<br>Esegui la migrazione della macchina virtuale accesa |
| Inventario macchina virtuale |  Crea da esistente<br>Creazione di un nuovo sito<br>Spostamento<br>Rimuovi | 
| Interazione tra macchine virtuali |  Configurare supporti CD<br>Interazione tra console<br>Connessione del dispositivo<br>Spegnimento<br>Accensione<br>Reset<br>Sospendi<br>Installazione degli strumenti | 
| Configurazione macchina virtuale |  Aggiungi disco esistente<br>Aggiungi nuovo disco<br>Aggiungi o Rimuovi<br>Rimuovi disco<br>Funzionalità avanzate<br>Cambia Conteggio CPU<br>Cambia risorsa<br>Estendi disco virtuale<br>Rilevamento modifiche disco<br>Memoria<br>Modificare le impostazioni del dispositivo<br>Rinomina<br>Set Annotation (versione 5,0 e successive)<br>Impostazioni<br>Selezione host file |
| Provisioning |  Personalizza<br>Clona modello<br>Clona macchina virtuale<br>Modello di distribuzione<br>Leggi le specifiche di personalizzazione |
| Stato macchina virtuale | Crea snapshot<br>Rimuovi snapshot<br>Ripristina snapshot |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Installare automazione di vRealize nell'ambiente locale

1. Accedere al dispositivo vRealize Automation IaaS server come amministratore IaaS creato automaticamente dal supporto AVS.
2. Distribuire un agente vSphere per l'endpoint di automazione vRealize.
    1. Passare a https://*LRV-URL*: 5480/Installer, dove *LRV-URL* è l'URL usato per accedere all'interfaccia utente di amministrazione di automazione vRealize.
    2. Per scaricare il programma di installazione, fare clic sul **programma di installazione di IaaS** .<br>
    La convenzione di denominazione per il file del programma di installazione è setup_@5480.exe*LRV-URL* .
    3. Eseguire il programma di installazione. Nella schermata iniziale fare clic su **Avanti**.
    4. Accettare il contratto di licenza e fare clic su **Avanti**.
    5. Fornire le informazioni di accesso, fare clic su **Accept certificate**, quindi fare clic su **Next**.
    ![le credenziali di LRV](media/configure-vra-endpoint-login.png)
    6. Selezionare **installazione personalizzata** e **agenti proxy** , quindi fare clic su **Avanti**.
    ![tipo di installazione di LRV](media/configure-vra-endpoint-install-type.png)
    7. Immettere le informazioni di accesso al server IaaS e fare clic su **Avanti**. Se si utilizza Active Directory, immettere il nome utente in formato **dominio\utente** . In caso contrario, utilizzare **user@domain** formato.
    informazioni di accesso ![LRV](media/configure-vra-endpoint-account.png)
    8. Per le impostazioni proxy, immettere **vSphere** per **tipo di agente**. Immettere un nome per l'agente.
    9. Immettere il nome di dominio completo del server IaaS nell' **host del servizio di gestione** e nei campi host del **servizio Web di gestione modelli** . Fare clic su **test** per testare la connessione per ognuno dei valori FQDN. Se il test ha esito negativo, modificare le impostazioni DNS in modo da risolvere il nome host del server IaaS.
    10. Immettere un nome per l'endpoint server vCenter per il cloud privato AVS. Registrare il nome da utilizzare in un secondo momento nel processo di configurazione.

        ![proxy di installazione di LRV](media/configure-vra-endpoint-proxy.png)

    11. Fare clic su **Avanti**.
    12. Fare clic su **Installa**.

## <a name="configure-the-vsphere-agent"></a>Configurare l'agente vSphere

1. Passare a https://*LRV-URL*/vCAC e accedere come **ConfigurationAdmin**.
2. Selezionare **infrastruttura** > **endpoint** > **endpoint**.
3. Selezionare **nuovo** > **Virtual** > **vSphere**.
4. Immettere il nome dell'endpoint vSphere specificato nella procedura precedente.
5. Per **Indirizzo**, immettere l'URL del server vCenter AVS private cloud nel formato https://*vCenter-FQDN*/SDK, dove *vCenter-FQDN* è il nome del server vCenter.
6. Immettere le credenziali per l'utente amministratore di vRealize Automation IaaS che è stato creato automaticamente dal supporto AVS.
7. Fare clic su **Test connessione** per convalidare le credenziali utente. Se il test ha esito negativo, verificare l'URL, le informazioni sull'account e il nome e il test dell' [endpoint](#verify-the-endpoint-name) .
8. Dopo un test riuscito, fare clic su **OK** per creare l'endpoint vSphere.
    ![l'accesso alla configurazione dell'endpoint di LRV](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Verificare il nome dell'endpoint

Per identificare il nome dell'endpoint del server vCenter corretto, procedere come segue:

1. Aprire un prompt dei comandi nell'appliance IaaS.
2. Passare alla directory C:\Program Files (x86) \VMware\vCAC\Agents\agent-name, dove *Agent-Name* è il nome assegnato all'endpoint del server vCenter.
3. Eseguire il comando seguente.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

L'output è simile al seguente. Il valore del campo `managementEndpointName` è il nome dell'endpoint.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
