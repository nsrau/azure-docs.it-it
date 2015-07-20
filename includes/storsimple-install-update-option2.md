
#### Per installare l'aggiornamento 1 dal portale di gestione di Azure

1. Nel portale di gestione, accedere alla pagina **Dispositivi** e selezionare il proprio dispositivo.
 
2. Andare a **Dispositivi** > **Configura**.

3. In **Interfacce di rete**, individuare l'interfaccia di rete con gateway assegnato. Si tratta dell'unica interfaccia di rete con valori diversi da DATA 0.

4. Deselezionare l'impostazione del gateway. Dal momento che le impostazioni del gateway sono necessarie nell'interfaccia di rete abilitata al cloud, è opportuno disattivare l'accesso cloud per tale interfaccia al fine di deselezionare l'impostazione.

5. Ripetere il passaggio 4 per qualsiasi altra interfaccia di rete con gateway assegnato (tranne DATA 0).

6. Salvare la configurazione modificata.

7. A questo punto è possibile [utilizzare il portale di gestione per installare l'aggiornamento 1](#use-the-management-portal-to-install-update-1).

<!---HONumber=July15_HO2-->