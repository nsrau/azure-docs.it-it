---
title: Risolvere i problemi di diagnostica della connettività in SSIS Integration Runtime
description: Questo articolo fornisce indicazioni sulla risoluzione dei problemi per la diagnostica della connettività in SSIS Integration Runtime
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172718"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>Risolvere i problemi di diagnostica della connettività in SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Se si verificano problemi di connettività durante l'esecuzione di pacchetti SSIS nel runtime di integrazione SSIS, soprattutto se il runtime di integrazione SSIS è stato aggiunto alla rete virtuale di Azure. È possibile provare a diagnosticare autonomamente i problemi usando questa funzionalità di connettività di diagnostica nella pagina Monitoraggio di runtime di integrazione SSIS del portale di Azure Data Factory. 

 ![Pagina monitoraggio-diagnostica ](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![ pagina Monitoraggio connettività-test connessione](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
Questo articolo include gli errori più comuni che possono essere riscontrati durante il test della connessione nel runtime di integrazione SSIS. Vengono descritte le possibili cause e le azioni per la risoluzione degli errori. 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>Errori comuni, possibili cause e soluzioni di raccomandazione

### <a name="error-code-invalidinput"></a>Codice di errore: InvalidInput.
* **Messaggio di errore**: verificare che l'input sia corretto.
* **Possibili cause:** L'input non è corretto.
* **Raccomandazione:** Controllare l'input.

### <a name="error-code-firewallornetworkissue"></a>Codice di errore: FirewallOrNetworkIssue.
* **Messaggio di errore**: verificare che la porta sia aperta nel firewall/server/NSG e che la rete sia stabile.
* **Possibili cause:** 
  * Il server non apre questa porta.
  * Al gruppo di sicurezza di rete è stato negato il traffico in uscita su questa porta
  * Il firewall di appliance virtuale di Azure/locale non apre questa porta.
* **Consiglio:** 
  * Aprire la porta sul server.
  * Aggiornare il gruppo di sicurezza di rete per consentire il traffico in uscita su questa porta.
  * Aprire questa porta nel firewall appliance virtuale di Azure o in un firewall locale.

### <a name="error-code-misconfigureddnssettings"></a>Codice di errore: MisconfiguredDnsSettings.
* **Messaggio di errore**: se si usa il proprio server DNS nel VNet aggiunto dal Azure-SSIS IR, verificare che sia in grado di risolvere il nome host.
* **Possibili cause:** 
  *  Il problema del DNS personalizzato
  *  Non si usa un nome di dominio completo (FQDN) per il nome host privato
* **Consiglio:** 
  *  Correggere il problema DNS personalizzato per assicurarsi che sia in grado di risolvere il nome host.
  *  Usare il nome di dominio completo (FQDN), ad esempio usare <your_private_server>. contoso.com anziché <your_private_server>, in quanto Azure-SSIS IR non aggiungerà automaticamente il proprio suffisso DNS.

### <a name="error-code-servernotallowremoteconenction"></a>Codice di errore: ServerNotAllowRemoteConenction.
* **Messaggio di errore**: verificare che il server consenta le connessioni TCP remote tramite questa porta.
* **Possibili cause:** 
  *  Il firewall del server non consente le connessioni TCP remote.
  *  Il server non è online.
* **Consiglio:** 
  *  Consente le connessioni TCP remote nel firewall del server.
  *  Avviare il server.
   
### <a name="error-code-misconfigurednsgsettings"></a>Codice di errore: MisconfiguredNsgSettings.
* **Messaggio di errore**: verificare che il NSG di VNet consenta il traffico in uscita attraverso questa porta. Se si usa Azure ExpressRoute e o una UDR, verificare che questa porta sia aperta nel firewall/server.
* **Possibili cause:** 
  *  Al gruppo di sicurezza di rete viene negato il traffico in uscita su questa porta.
  *  Il firewall di appliance virtuale di Azure/locale non apre questa porta.
* **Consiglio:** 
  *  Aggiornare il gruppo di sicurezza di rete per consentire il traffico in uscita su questa porta.
  *  Aprire questa porta nel firewall appliance virtuale di Azure o in un firewall locale.

### <a name="error-code-genericissues"></a>Codice di errore: GenericIssues.
* **Messaggio di errore**: test della connessione non riuscito a causa di problemi generici.
* **Possibili cause:** Si è verificato un problema temporaneo generale della connessione di test.
* **Raccomandazione:** Ripetere la connessione di test in un secondo momento. Se il nuovo tentativo non è utile, contattare il team di supporto di Azure Data Factory.


### <a name="error-code-pspingexecutiontimeout"></a>Codice di errore: PSPingExecutionTimeout.
* **Messaggio di errore**: verifica il timeout della connessione. riprovare più tardi.
* **Possibili cause:** Timeout della connettività di test.
* **Raccomandazione:** Ripetere la connessione di test in un secondo momento. Se il nuovo tentativo non è utile, contattare il team di supporto di Azure Data Factory.

### <a name="error-code-networkinstable"></a>Codice di errore: NetworkInstable.
* **Messaggio di errore**: la connessione di test ha avuto esito positivo a causa di un'instabilità della rete.
* **Possibili cause:** Problema di rete temporaneo.
* **Raccomandazione:** Controllare se la rete del server o del firewall è stabile.

## <a name="next-steps"></a>Passaggi successivi

- Distribuire i pacchetti. Per altre informazioni, vedere [Distribuire un progetto SSIS con SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Eseguire i pacchetti. Per altre informazioni, vedere [Eseguire pacchetti SSIS in Azure con SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Pianificare i pacchetti. Per altre informazioni, vedere [pianificare pacchetti SSIS in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).

