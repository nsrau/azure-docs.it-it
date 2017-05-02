---
title: Gateway dati locale | Microsoft Docs
description: "Un gateway locale è necessario se il server Analysis Services di Azure si connette a origini dati locali."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: c7127f4bd89bf00a4ef72e54dc7694a1766e5762
ms.lasthandoff: 04/18/2017


---
# <a name="on-premises-data-gateway"></a>Gateway dati locale
Il gateway dati locale svolge la funzione di ponte, garantendo il trasferimento sicuro dei dati tra le origini dati locali e il server Azure Analysis Services nel cloud.

Un gateway viene installato in un computer della rete. È necessario installare un gateway per ciascun server Azure Analysis Services presente nella sottoscrizione di Azure. Se, ad esempio, nella sottoscrizione di Azure sono presenti due server che si connettono a origini dati locali, è necessario installare un gateway in due diversi computer della rete.

## <a name="requirements"></a>Requisiti
**Requisiti minimi:**

* .NET Framework 4.5
* Versione a 64 bit di Windows 7 o Windows Server 2008 R2 (o versione successiva)

**Consigliato:**

* 8 CPU core
* 8 GB di memoria
* Windows 2012 R2 versione a 64 bit (o versione successiva)

**Considerazioni importanti:**

* Il gateway non può essere installato in un controller di dominio.
* In un singolo computer può essere installato un solo gateway.
* Installare il gateway in un computer che rimane attivo e non passa alla modalità di sospensione. Se il computer non è attivo, il server Azure Analysis Services non può connettersi alle origini dati locali per aggiornare i dati.
* Non installare il gateway in un computer connesso alla rete in modalità wireless. È infatti possibile che si verifichi un calo delle prestazioni.
* Per modificare il nome del server per un gateway che è già stato configurato, è necessario reinstallare e configurare un nuovo gateway.
* In alcuni casi, i modelli tabulari che si connettono alle origini dati usando provider nativi quali SQL Server Native Client (SQLNCLI11) possono restituire un errore. Per altre informazioni, vedere [Connessioni alle origini dati](analysis-services-datasource.md).

## <a name="supported-on-premises-data-sources"></a>Origini dati locali supportate
Per la versione di anteprima, il gateway supporta le connessioni tra il server Azure Analysis Services e le origini dati locali seguenti:

* SQL Server
* SQL Data Warehouse
* APS
* Oracle
* Teradata

## <a name="download"></a>Scaricare
 [Scaricare il gateway](https://aka.ms/azureasgateway)

## <a name="install-and-configure"></a>Installare e configurare
1. Eseguire l'installazione.
2. Scegliere un percorso di installazione e accettare le condizioni di licenza.
3. Accedere ad Azure.
4. Specificare il nome del server Azure Analysis Server. È possibile specificare soltanto un server per gateway. Fare clic su **Configura** per iniziare.

    ![Accesso ad Azure](./media/analysis-services-gateway/aas-gateway-configure-server.png)

## <a name="how-it-works"></a>Funzionamento
Il gateway viene eseguito come servizio Windows, **Gateway dati locale**, in un computer della rete dell'organizzazione. Il gateway installato per l'uso con Azure Analysis Services è basato sullo stesso gateway usato per altri servizi, ad esempio Power BI, ma con alcune differenze riguardo alla configurazione.

![Funzionamento](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Le query e il flusso di dati funzionano nel modo seguente:

1. Il servizio cloud crea una query con le credenziali crittografate per l'origine dati locale. La query viene inviata a una coda per l'elaborazione da parte del gateway.
2. Il servizio cloud del gateway analizza la query e inserisce la richiesta nel [bus di servizio di Azure](https://azure.microsoft.com/documentation/services/service-bus/).
3. Il gateway dati locale esegue il polling del bus di servizio per le richieste in sospeso.
4. Il gateway riceve la query, decrittografa le credenziali e si connette alle origini dati usando tali credenziali.
5. Invia quindi la query all'origine dati per l'esecuzione.
6. I risultati vengono inviati dall'origine dati al gateway e quindi al servizio cloud.

## <a name="windows-service-account"></a>Account del servizio Windows
Il gateway dati locale è configurato per usare *NT SERVICE\PBIEgwService* come credenziale di accesso al servizio Windows. Per impostazione predefinita, dispone del diritto di accesso come servizio nel contesto del computer in cui viene installato il gateway. Questa credenziale non coincide con l'account usato per la connessione alle origini dati locali o con l'account Azure.  

Se si verificano problemi di autenticazione con il server proxy, può essere necessario trasformare l'account del servizio Windows nell'account di un utente del dominio o nell'account di un servizio gestito.

## <a name="ports"></a>Porte
Il gateway crea una connessione in uscita al bus di servizio di Azure. Comunica sulle porte in uscita seguenti: TCP 443 (impostazione predefinita), 5671, 5672 e da 9350 a 9354.  Non sono richieste porte in ingresso.

È consigliabile inserire nell'elenco degli elementi consentiti gli indirizzi IP per l'area dati del firewall. È possibile scaricare l'[elenco degli indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). L'elenco viene aggiornato ogni settimana.

> [!NOTE]
> Gli indirizzi IP inclusi nell'elenco degli indirizzi IP dei data center di Azure sono nella notazione CIDR. Ad esempio, 10.0.0.0/24 non significa da 10.0.0.0 a 10.0.0.24. Per altre informazioni, vedere [CIDR notation](http://whatismyipaddress.com/cidr) (Notazione CIDR).
>
>

Di seguito sono indicati i nomi di dominio completi usati dal gateway.

| Nomi di dominio | Porte in uscita | Descrizione |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP usato per scaricare il programma di installazione. |
| *.powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Listener in Inoltro del Bus di servizio su TCP (richiede 443 per l'acquisizione del token di Controllo di accesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Usato per testare la connettività a Internet se il gateway non è raggiungibile dal servizio Power BI. |
| *.microsoftonline-p.com |443 |Usato per l'autenticazione, a seconda della configurazione. |

### <a name="forcing-https-communication-with-azure-service-bus"></a>Forzare la comunicazione HTTPS con il bus di servizio di Azure
È possibile forzare il gateway a comunicare con il bus di servizio di Azure usando HTTPS al posto di TCP diretto. Questa operazione, tuttavia, può ridurre le prestazioni in misura significativa. È necessario modificare il file *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config*. Modificare il valore da `AutoDetect` in `Https`. Per impostazione predefinita, questo file si trova in *C:\Programmi\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>Risoluzione dei problemi
Dietro le quinte, il gateway di dati locale usato per la connessione alle origini dati locali di Azure Analysis Services è lo stesso gateway usato con Power BI.

Se si verificano problemi durante l'installazione e la configurazione di un gateway, vedere [Troubleshooting the Power BI Gateway](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/) (Risoluzione dei problemi del gateway Power BI). In caso di problemi con il firewall, vedere le sezioni relative al firewall o al proxy.

Se si verificano problemi di proxy con il gateway, vedere [Configuring proxy settings for the Power BI Gateways](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md) (Configurazione delle impostazioni del proxy per i gateway Power BI).

### <a name="telemetry"></a>Telemetria
La telemetria può essere usata per il monitoraggio e la risoluzione dei problemi. 

**Per attivare la telemetria**

1.    Verificare la directory del client gateway dati locale nel computer. In genere è %systemdrive%\Programmi\gateway dati locale. Oppure è possibile aprire una console dei servizi e verificare il percorso al file eseguibile, una proprietà del servizio gateway dati locale.
2.    Nel file Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config nella directory del client modificare l'impostazione SendTelemetry su true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.    Salvare le modifiche e riavviare il servizio Windows gateway dati locale.




## <a name="next-steps"></a>Passaggi successivi
* [Gestire Analysis Services](analysis-services-manage.md)
* [Ottenere dati da Azure Analysis Services](analysis-services-connect.md)

