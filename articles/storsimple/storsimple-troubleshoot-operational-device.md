<properties 
   pageTitle="Risoluzione dei problemi relativi a un dispositivo StorSimple operativo"
   description="Viene descritto come diagnosticare e correggere gli errori che si verificano su un dispositivo StorSimple operativo."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/09/2015"
   ms.author="v-sharos" />

# Risoluzione dei problemi relativi a un dispositivo StorSimple operativo

## Panoramica

Questo articolo fornisce utili indicazioni sulla risoluzione dei problemi riguardo la configurazione che possono verificarsi dopo che il dispositivo StorSimple è distribuito e operativo. Vengono descritti problemi comuni, le possibili cause e le procedure consigliate per la risoluzione dei problemi che potrebbero verificarsi durante l'esecuzione di StorSimple. Queste informazioni si applicano sia al dispositivo fisico locale StorSimple che al dispositivo virtuale StorSimple.

## Procedura di configurazione guidata per i dispositivi operativi

È possibile utilizzare la configurazione guidata (Invoke-HcsSetupWizard) per controllare la configurazione del dispositivo e intraprendere l'azione correttiva, se necessario.

Quando si esegue la configurazione guidata su un dispositivo precedentemente configurato e operativo, il flusso del processo è differente. È possibile modificare le seguenti voci:

- Indirizzo IP, subnet mask e gateway
- Server DNS primario
- Server NTP primario
- Configurazione del proxy Web facoltativa

La configurazione guidata non esegue le operazioni correlate alla raccolta della password alla registrazione del dispositivo.

## Errori che si verificano durante le esecuzioni successive della configurazione guidata

Nella tabella seguente vengono descritti gli errori che potrebbero verificarsi quando si esegue l'installazione guidata su un dispositivo operativo, le cause di errori e le azioni consigliate per la risoluzione.

| No. | Messaggio di errore o condizione | Possibili cause | Azione consigliata |
| --- | -------------------------- | --------------- | ------------------ |
| 1 | Errore 350032: il dispositivo è già stato disattivato. | Questo errore verrà visualizzato se si esegue la configurazione guidata su un dispositivo disattivato. | [Contattare il supporto tecnico Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) per i passaggi successivi. Un dispositivo disattivato non può essere messo in servizio. Prima di poter attivare nuovamente il dispositivo, potrebbe essere necessario un ripristino delle impostazioni predefinite. |
| 2 | Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (eccezione da HRESULT: 0x80070001) | L'aggiornamento del server DNS in corso ha esito negativo. Le impostazioni DNS sono impostazioni globali e vengono applicate a tutte le interfacce di rete abilitate. | Abilitare l'interfaccia e applicare nuovamente le impostazioni DNS. Siccome queste impostazioni sono globali, ciò potrebbe interferire con la rete per le altre interfacce abilitate. |
| 3 | Il dispositivo apparirà online nel portale del servizio StorSimple Manager ma quando si tenta di completare l'installazione minima e salvare la configurazione, l'operazione ha esito negativo. | Durante l'installazione iniziale, il proxy Web non è stato configurato, anche se era disponibile un server proxy effettivo. | Utilizzare il cmdlet [Test-HcsmConnection](https://msdn.microsoft.com/library/azure/eedae62d-0957-4005-b346-9248724f90e0#sec05) per individuare l'errore. [Contattare il supporto tecnico Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) se non si è in grado di risolvere il problema. |
| 4 | Invoke-HcsSetupWizard: il valore non è compreso nell'intervallo previsto. | L'errore viene generato da una subnet mask non corretta. Le possibili cause sono: <ul><li> La subnet mask è assente o vuota.</li><li>Il formato del prefisso Ipv6 non è corretto.</li><li>L'interfaccia è abilitata per il cloud ma il gateway è assente o non corretto.</li></ul>Considerare che DATI 0 è automaticamente abilitata per il cloud se configurata tramite la configurazione guidata. | Per determinare il problema, utilizzare la subnet 0.0.0.0 o 256.256.256.256 ed esaminare l'output. Immettere i valori corretti per la subnet mask, un gateway e un prefisso Ipv6, se necessario. |
 
## Passaggi successivi
Se il problema non viene risolto, [contattare il supporto tecnico Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) per assistenza.

<!---HONumber=July15_HO4-->