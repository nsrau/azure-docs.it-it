#### Ripristinare il dispositivo fisico per il dispositivo virtuale StorSimple

1. Verificare che il contenitore del volume di cui si desidera eseguire il failover sia associato alle snapshot cloud.

2. Nella pagina **Dispositivo**, fare clic sulla scheda **Contenitori di volumi**.

3. Selezionare un contenitore del volume di cui si desidera eseguire il failover a un dispositivo virtuale. Fare clic su contenitore del volume per visualizzare l'elenco dei volumi presenti in questo contenitore. Selezionare un volume e fare clic su **Offline** per passare il volume offline. Ripetere questo processo per tutti i volumi nel contenitore di volumi.

4. Ripetere il passaggio precedente per tutti i contenitori di volumi di cui si desidera eseguire il failover al dispositivo virtuale.

5. Nella pagina **Dispositivo**, selezionare il dispositivo di cui eseguire il failover, quindi fare clic su **Failover** per aprire la procedura guidata **Failover del dispositivo**.

6. In **Scegli contenitore del volume di cui eseguire il failover**, selezionare i contenitori di volumi per i quali si desidera eseguire il failover. Affinché sia visualizzato in questo elenco, il contenitore del volume deve contenere una snapshot cloud e deve essere offline. Se per errore non viene visualizzato un contenitore del volume, annullare la procedura guidata e verificare che sia offline.

7. Nella pagina successiva, in **Scegli un dispositivo di destinazione per i volumi dei contenitori selezionati**, selezionare il dispositivo virtuale dall'elenco a discesa dei dispositivi disponibili. Solo i dispositivi dotati di capacità disponibile vengono visualizzati nell'elenco.

8. Controllare tutte le impostazioni di failover nella pagina **Conferma failover**. Se sono corrette, fare clic sull'icona del segno di spunta.

A questo punto, viene avviato il processo di failover. Al termine del processo di failover, accedere alla pagina Dispositivi e selezionare il dispositivo virtuale che è stato utilizzato come destinazione del processo di failover. Andare alla pagina Contenitori di volumi. Dovrebbero essere elencati tutti i contenitori di volumi, insieme ai volumi del dispositivo precedente.

<!---HONumber=AcomDC_1217_2015-->