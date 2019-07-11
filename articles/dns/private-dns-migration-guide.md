---
title: Migrazione di zone private legacy di DNS di Azure nel nuovo modello di risorsa
description: Questa guida offre istruzioni dettagliate su come eseguire la migrazione di zone DNS private legacy nel modello di risorsa più recente
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: e7ebbf35cd572601f02a69930b58811686a92c86
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276319"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrazione di zone private legacy di DNS di Azure nel nuovo modello di risorsa

È disponibile un nuovo modello di risorsa/API per le zone private di DNS di Azure come parte della versione di aggiornamento in anteprima. L'aggiornamento in anteprima offre nuove funzionalità ed elimina diverse limitazioni e restrizioni rispetto all'anteprima pubblica iniziale. Tuttavia, questi vantaggi non sono disponibili per le zone DNS private create tramite l'API legacy. Per ottenere i vantaggi della nuova versione, è necessario eseguire la migrazione delle risorse delle zone DNS private legacy nel nuovo modello di risorsa. Il processo di migrazione è semplice ed è disponibile uno script di PowerShell per automatizzarlo. Questa guida offre istruzioni dettagliate per la migrazione delle zone private di DNS di Azure nel nuovo modello di risorsa.

## <a name="prerequisites"></a>Prerequisiti

Verificare di aver installato la versione più recente di Azure PowerShell. Per altre informazioni su Azure PowerShell (Az) e su come installarlo, visitare la pagina all'indirizzo https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Assicurarsi di avere a disposizione il modulo Az.PrivateDns per la versione di Azure PowerShell installata. Per installare questo modulo, aprire una finestra di PowerShell con privilegi elevati (modalità di amministrazione) e immettere il comando seguente

```powershell
Install-Module -Name Az.PrivateDns -AllowPrerelease
```

>[!IMPORTANT]
>Il processo di migrazione è completamente automatizzato e non causa tempo di inattività. Tuttavia, se si usano zone private di DNS di Azure (anteprima) in un ambiente di produzione critico, è necessario eseguire il processo di migrazione presentato di seguito durante un intervallo di tempo per la manutenzione pianificata. Assicurarsi di non modificare la configurazione o i set di record di una zona DNS privata durante l'esecuzione dello script di migrazione.

## <a name="installing-the-script"></a>Installazione dello script

Aprire una finestra di PowerShell con privilegi elevati (modalità di amministrazione) e immettere il comando seguente

```powershell
install-script PrivateDnsMigrationScript
```

Immettere "A" quando viene chiesto se installare lo script

![Installazione dello script](./media/private-dns-migration-guide/install-migration-script.png)

È anche possibile ottenere manualmente la versione più recente dello script di PowerShell all'indirizzo https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

## <a name="running-the-script"></a>Esecuzione dello script

Eseguire il comando seguente per eseguire lo script

```powershell
PrivateDnsMigrationScript.ps1
```

![Esecuzione dello script](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Immettere l'ID sottoscrizione e accedere ad Azure

Verrà chiesto di immettere l'ID sottoscrizione che contiene le zone DNS private di cui si intende eseguire la migrazione. Verrà chiesto di accedere all'account Azure. Completare l'accesso in modo che lo script possa accedere alle risorse delle zone DNS private nella sottoscrizione.

![Accedere ad Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Selezionare le zone DNS di cui si vuole eseguire la migrazione

Lo script ottiene l'elenco di tutte le zone DNS private nella sottoscrizione e chiede di confermare quelle di cui eseguire la migrazione. Immettere "A" per eseguire la migrazione di tutte le zone DNS private. Dopo aver eseguito questo passaggio, lo script creerà nuove zone DNS private usando il nuovo modello di risorsa e copierà i dati nella nuova zona DSN. Questo passaggio non modifica in alcun modo le zone DNS private esistenti.

![Selezionare le zone DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Passaggio della risoluzione DNS alle nuove zone DNS

Dopo che le zone e i record sono stati copiati nel nuovo modello di risorsa, lo script chiede se passare la risoluzione DNS alle nuove zone DNS. Questo passaggio rimuove l'associazione tra le zone DNS private legacy e le reti virtuali. Quando la zona legacy viene scollegata dalle reti virtuali, le nuove zone DNS create nel passaggio precedente acquisiscono automaticamente la risoluzione DNS per le reti virtuali.

Selezionare "A" per spostare la risoluzione DNS per tutte le reti virtuali.

![Passaggio della risoluzione dei nomi](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Verificare la risoluzione DNS

Prima di procedere, verificare che la risoluzione DNS nelle zone DNS funzioni come previsto. È possibile accedere alle macchine virtuali di Azure ed eseguire una query nslookup sulle zone di cui è stata eseguita la migrazione per verificare il funzionamento della risoluzione DNS.

![Verificare la risoluzione dei nomi](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Se si riscontra la mancata risoluzione delle query DNS, attendere qualche minuto e ripetere la query. Se le query DNS funzionano come previsto, immettere "Y" quando lo script chiede se rimuovere la rete virtuale dalla zona DNS privata.

![Confermare la risoluzione dei nomi](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Se a causa di un motivo qualsiasi la risoluzione DNS sulle zone di cui è stata eseguita la migrazione non funziona, immettere "N" nel passaggio precedente perché lo script riporti la risoluzione DNS alle zone legacy. Creare un ticket di supporto per chiedere assistenza per la migrazione delle zone DNS.

## <a name="cleanup"></a>Pulizia

Questo passaggio elimina le zone DNS legacy e deve essere eseguito solo dopo aver verificato che la risoluzione DNS funzioni come previsto. Verrà chiesto di eliminare ogni zona DNS privata. Immettere "Y" in ogni prompt dopo aver verificato il funzionamento della risoluzione DNS per le zone.

![Eseguire la pulizia](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Aggiornare l'automazione

Se si usa l'automazione, tra cui modelli, script di PowerShell o codice personalizzato sviluppato con SDK, è necessario aggiornare l'automazione in modo da usare il nuovo modello di risorsa per le zone DNS private. Di seguito vengono forniti i collegamenti alla nuova documentazione dell'interfaccia della riga di comando, di PowerShell e dell'SDK per DNS.
* [API REST delle zone private di DNS di Azure](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Interfaccia della riga di comando per le zone private di DNS di Azure](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [PowerShell per le zone private di DNS di Azure](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [SDK per le zone private di DNS di Azure](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Ulteriore assistenza

Creare un ticket di supporto se è necessaria ulteriore assistenza per il processo di migrazione o se per qualsiasi motivo i passaggi elencati sopra non hanno funzionato. Includere il file di trascrizione generato dallo script di PowerShell nel ticket di supporto.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una zona privata in DNS di Azure tramite [Azure PowerShell](./private-dns-getstarted-powershell.md) o l'[interfaccia della riga di comando di Azure](./private-dns-getstarted-cli.md).

* Consultare alcuni [scenari comuni sulle zone private](./private-dns-scenarios.md) che possono essere realizzati con le zone private in DNS di Azure.

* Per domande e risposte comuni sulle zone private in DNS di Azure, tra cui i comportamenti specifici previsti per determinati tipi di operazioni, vedere [Domande frequenti su DNS privato](./dns-faq-private.md).

* Per informazioni sui record e le zone DNS visitare la pagina [Panoramica delle zone e dei record DNS](dns-zones-records.md).

* Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
