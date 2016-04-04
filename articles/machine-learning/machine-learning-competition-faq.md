<properties
	pageTitle="Domande frequenti relative alla competizione su Machine Learning | Microsoft Azure"
	description="Domande frequenti sui concorsi di Microsoft Cortana Analytics Suite."
	services="machine-learning"
	documentationCenter=""
	authors="hning86"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/11/2016"
	ms.author="haining;chlovel;garye"/>

# Domande frequenti sui concorsi di Microsoft Cortana Analytics Suite

**Dove è possibile porre domande generali relative all’analisi scientifica dei dati?**

Si può usare il forum ufficiale di [Microsoft Azure Machine Learning](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning).

**Come si partecipa a una competizione?**

Creare un account gratuito o a pagamento in [Microsoft Azure Machine Learning](https://studio.azureml.net/?selectAccess=true&o=2%22%20\t%20%22_blank). Seguire l'esercitazione sulle competizioni e guardare il breve video.

**La competizione è riservata ai data scientist?**

No. Sono invitati a partecipare alla competizione gli appassionati di analisi scientifica dei dati, ma anche chi è curioso sebbene non esperto della materia. Sono stati preparati documenti di supporto per consentire a chiunque di partecipare.

**Quanto tempo richiederà?**

È possibile prendere parte alla competizione e creare un modello di dati in 10-15 minuti. Per perfezionare l’esperimento predittivo si può impiegare più di 15 minuti, in base alla familiarità con Machine Learning.

**Come viene calcolato il punteggio? Viene calcolato in modo diverso durante la competizione e dopo la sua conclusione? Cosa sono i punteggi pubblici e privati?**

1.  L'intero set di dati è stato suddiviso in modo casuale con una stratificazione in dati di training (60%) e dati di test (rimanente 40%). La suddivisione casuale è stratificata sull’area geografica, il segmento e il sottogruppo, per garantire che le distribuzioni delle etichette dei dati di training e di test siano coerenti in ogni area.  

2.  I dati di training sono stati caricati e forniti come parte dell'esperimento iniziale nella configurazione del modulo Reader.

3.  I dati di test sono stati suddivisi ulteriormente in dati di test pubblici e privati, utilizzando la stessa stratificazione (60% per i dati di test pubblici e 40% per i dati di test privati).

4.  I dati di test pubblici sono stati utilizzati per il turno iniziale di assegnazione dei punteggi. Il risultato è il punteggio pubblico, quello che si è ottenuto nella cronologia di invio in seguito all’invio del contributo. Questa operazione è stata eseguita ogni volta che è stato inviato un contributo. Questo punteggio pubblico viene usato per il posizionamento del concorrente nella classifica pubblica.

5.  I dati di test privati sono stati utilizzati per il turno finale di assegnazione dei punteggi dopo la conclusione della competizione. Questo è il punteggio privato.

6.  Per calcolare i punteggi privati, per ogni partecipante sono stati selezionati automaticamente 5 contributi fra quelli con i punteggi pubblici più alti. Il contributo con il punteggio privato più alto è stato selezionato per la classificazione finale, che ha determinato i vincitori.

**La competizione è a pagamento?**

No, è possibile impostare un’area di lavoro gratuita in Microsoft Azure Machine Learning per partecipare alla competizione. Tenere presente che gli account guest non consentono di salvare i dati, pertanto assicurarsi di inviare il contributo prima del termine delle 8 ore gratuite.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Come si effettua la conversione in sottoscrizione a pagamento?**

È possibile acquistare o convertire la sottoscrizione da questa pagina su Microsoft Azure Marketplace. Vedere [questa pagina](https://azure.microsoft.com/pricing/details/machine-learning/) per domande frequenti sui prezzi.

**Cos’è il pulsante Prova Belize?**

(Includere le istruzioni relative al portale dei servizi Web e una schermata con i passaggi per tornare indietro)

**Cosa accade se si vince un concorso?**

Microsoft verificherà i risultati della classifica privata e contatterà i vincitori. Assicurarsi che l'indirizzo di posta elettronica del profilo utente sia aggiornato.

**In che modo il denaro del premio viene consegnato ai vincitori?**

I vincitori della competizione devono firmare una dichiarazione di idoneità, autorizzazione e liberatoria. Questo modulo riporta le regole della competizione. I vincitori devono compilare il modulo fiscale W-9 se sono contribuenti degli Stati Uniti o il modulo W-8BEN se non sono contribuenti degli Stati Uniti.

<!---HONumber=AcomDC_0323_2016-->