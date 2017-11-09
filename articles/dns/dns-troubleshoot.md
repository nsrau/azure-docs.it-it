---
title: Guida alla risoluzione dei problemi di DNS di Azure | Documentazione Microsoft
description: Informazioni su come risolvere i problemi comuni di DNS di Azure
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: 
ms.assetid: 95b01dc3-ee69-4575-a259-4227131e4f9c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/20/2017
ms.author: jonatul
ms.openlocfilehash: 1d9bb681a864bdc3e5a2f9c9a531d9566b16ada4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-dns-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di DNS di Azure

Questa pagina fornisce informazioni sulla risoluzione dei problemi comuni di DNS di Azure.

Se questi passaggi non consentono di risolvere il problema, è anche possibile eseguire una ricerca o inserire una domanda nel [forum di supporto della community su MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork). In alternativa, aprire una richiesta di Supporto tecnico di Azure.


## <a name="i-cant-create-a-dns-zone"></a>Non è possibile creare una zona DNS

Per risolvere i problemi comuni, provare le procedure seguenti:

1.  Esaminare i log di controllo di DNS di Azure per determinare le cause dell'errore.
2.  Ogni nome di zona DNS deve essere univoco all'interno del relativo gruppo di risorse, ovvero due zone DNS con lo stesso nome non possono condividere uno stesso gruppo di risorse. Provare a usare un nome di zona o un gruppo di risorse diverso.
3.  Potrebbe essere visualizzato l'errore "È stato raggiunto o superato il numero massimo di zone nella sottoscrizione {ID sottoscrizione}." Usare una sottoscrizione di Azure diversa, eliminare alcune zone o contattare il supporto tecnico di Azure per aumentare il limite della sottoscrizione.
4.  Se viene visualizzato l'errore "La zona '{nome zona}' non è disponibile.", significa che il servizio DNS di Azure non è riuscito ad allocare i server dei nomi per questa zona DNS. Provare a usare un nome di zona diverso. In alternativa, se si è il proprietario del nome di dominio, contattare il supporto tecnico di Azure, che può allocare i server dei nomi per conto degli utenti.


### <a name="recommended-documents"></a>**Documenti consigliati**

[Zone e record DNS](dns-zones-records.md)
<br>
[Creare una zona DNS](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Non è possibile creare un record DNS

Per risolvere i problemi comuni, provare le procedure seguenti:

1.  Esaminare i log di controllo di DNS di Azure per determinare le cause dell'errore.
2.  Il set di record esiste già?  Il servizio DNS di Azure gestisce i record usando *set* di record, ovvero una raccolta di record dello stesso nome e dello stesso tipo. Se esiste già un record con lo stesso nome e dello stesso tipo, per aggiungere un altro record identico è consigliabile modificare il set di record esistente.
3.  Si sta tentando di creare un record al vertice della zona DNS (la "radice" della zona)? In questo caso, la convenzione DNS è quella di usare il carattere "@" come nome del record. Tenere presente inoltre che gli standard DNS non consentono record CNAME al vertice della zona.
4.  Si è verificato un conflitto CNAME?  Gli standard DNS non consentono un record CNAME con lo stesso nome di un record di un altro tipo. Se si ha già un record CNAME, la creazione di un record con lo stesso nome e di tipo diverso avrà esito negativo.  Analogamente, la creazione di un record CNAME avrà esito negativo se il nome corrisponde a un record esistente di tipo diverso. Per eliminare il conflitto, rimuovere l'altro record o scegliere un nome di record diverso.
5.  È stato raggiunto il limite relativo al numero di set di record consentiti in una zona DNS? Il numero corrente di set di record e il numero massimo di set di record vengono visualizzati nel portale di Azure, nella sezione delle proprietà relative alla zona. Se è stato raggiunto il limite, eliminare alcuni set di record oppure contattare il supporto tecnico di Azure per innalzare il numero massimo di set di record per la zona e riprovare. 


### <a name="recommended-documents"></a>**Documenti consigliati**

[Zone e record DNS](dns-zones-records.md)
<br>
[Creare una zona DNS](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Non riesco a risolvere un record DNS

La risoluzione dei nomi DNS è un processo articolato in più passaggi che può non riuscire per vari motivi. La procedura seguente consente di capire il motivo per cui non è possibile eseguire la risoluzione DNS per un record DNS in una zona ospitata nel servizio DNS di Azure.

1.  Verificare che i record DNS siano stati configurati correttamente nel servizio DNS di Azure. Esaminare i record DNS nel portale di Azure, controllando che il nome della zona, il nome del record e il tipo di record siano corretti.
2.  Verificare che i record DNS vengano risolti correttamente nei server dei nomi DNS di Azure.
    - Se si eseguono query DNS dal proprio computer locale, è possibile che vengano visualizzati risultati memorizzati nella cache che non riflettono lo stato corrente dei server dei nomi.  Nelle reti aziendali, inoltre, sono spesso presenti server proxy DNS che impediscono alle query DNS di essere reindirizzate a specifici server dei nomi.  Per evitare questi problemi, è possibile usare un servizio di risoluzione dei nomi basato sul Web come [digwebinterface](http://digwebinterface.com).
    - Assicurarsi di specificare i server dei nomi corretti per la propria zona DNS, così come sono riportati nel portale di Azure.
    - Verificare che siano corretti sia il nome DNS (è necessario specificare il nome completo, incluso il nome della zona) sia il tipo di record.
3.  Assicurarsi che il nome di dominio DNS sia stato correttamente [delegato ai server dei nomi DNS di Azure](dns-domain-delegation.md). Esistono [molti siti Web di terze parti da cui è possibile eseguire la convalida della delega DNS](https://www.bing.com/search?q=dns+check+tool). Trattandosi di un test della delega per la *zona*, è sufficiente specificare il nome della zona DNS, non il nome completo del record.
4.  Dopo aver completato la procedura appena descritta dovrebbe essere possibile risolvere correttamente il record DNS. Per verificarlo, è possibile usare nuovamente il servizio [digwebinterface](http://digwebinterface.com), questa volta specificando le impostazioni predefinite dei server dei nomi.


### <a name="recommended-documents"></a>**Documenti consigliati**

[Delegare un dominio al servizio DNS di Azure](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Come posso specificare il "servizio" e il "protocollo" per un record SRV?

Il servizio DNS di Azure gestisce i record come set di record, ovvero come una raccolta di record dello stesso nome e dello stesso tipo. Per un set di record SRV, il "servizio" e il "protocollo" devono essere specificati come parte del nome del set di record. Gli altri parametri SRV ("priorità", "peso", "porta" e "destinazione") vengono specificati separatamente per ogni record del set di record.

Nomi di record SRV di esempio ("sip" indica il nome del servizio, "tcp" il protocollo):

- \_sip.\_tcp (crea un set di record all'apice della zona)
- \_sip.\_tcp.sipservice (crea un set di record denominato "sipservice")

### <a name="recommended-documents"></a>**Documenti consigliati**

[Zone e record DNS](dns-zones-records.md)
<br>
[Creare set di record e record DNS mediante il portale di Azure](dns-getstarted-create-recordset-portal.md)
<br>
[SRV record type (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record) (Tipo di record SRV (Wikipedia))


## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [zone e sui record di DNS di Azure](dns-zones-records.md)
* Per iniziare a usare DNS di Azure, è necessario sapere come [creare una zona DNS](dns-getstarted-create-dnszone-portal.md) e come [creare record DNS](dns-getstarted-create-recordset-portal.md).
* Per eseguire la migrazione di una zona DNS esistente, è necessario saper [importare ed esportare un file di zona DNS](dns-import-export.md).

