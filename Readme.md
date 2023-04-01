# Systek CTF

Liste for å husk hva som må gjøres i forkant av et CTF-arrangement. 

## Avgjørelser som må taes: 

1. Skal vi kjøre eventet med team? 
    1. Hvor store skal teamene være?
    2. Skal team-medlemmene dele juice-shop instans? Dette må i så fall testes for å finne ut av hvordan opplevelsen blir 
    for de som er på samme team. Får alle opp en notifikasjon når 1 person på teamet finner et flagg? Deles scoreboard-seksjonen
       slik at challenges som er løst av andre teammedlemmer vises som løst hos alle teammedlemmer?
2. Hvor lenge skal eventet vare? 
3. Hvor mye skal vi gå gjennom i forkant? Vi må åpenbart bruke en del tid på å forklare juice-shop og scoring serveren. 
Forklare hvordan man går frem for å finne flagg i `juice-shop` og gå gjennom hvordan man fører poeng inn i RootTheBox.
4. Sikkerhet 101? (SQL Injection? XSS?)
5. Demo av løsning av en challenge. (scoreboard?) Og si fra at det å finne score board er første challenge.
6. Sende ut informasjon i forkant? Jeg ønsker ikke at deltakerene skal lese seg opp på juice-shop i forkant av eventet.

 
## Event-relaterte avgjørelser
1. Vi må sende ut påmelding til eventet god tid i forveien, slik at jeg kan forberede brukere til de som ønsker å delta.
   Jeg må også teste at vi er i stand til å spinne opp nok noder i digital ocean for at alle skal få tilgang.
2. Alle deltakere må ha med egen PC. Alt foregår på web, så OS burde være irrelevant. Browser burde være evergreen. 
Alt annet enn IE burde være ok. Kanskje noe trøbbel med Safari.
3. Sette dato for utsending av informasjon om event og påmelding
4. Sette dato for frist for påmelding. 
5. Sette dato for arrangering av eventet.
6. Skal vi sette opp brukere for deltakerene i forkant på scoring server? 
7. Skal vi spinne opp juice-shop instanser med team-navn og passkode i forkant? Det kan være noe ventetid med oppstart av 
   noder i k8s, men dette vil jeg jo teste i forkant så snart jeg vet hvor mange deltakere vi blir.
8. Skal vi ha betalte hint og hint url-er? Eller burde disse være gratis?   


## Tekniske forberedelser
Viktige lenker: 
1. [multijuicer](https://github.com/iteratec/multi-juicer)
2. [multijuicer for digital ocean](https://github.com/iteratec/multi-juicer/blob/master/guides/digital-ocean/digital-ocean.md)
3. [RootTheBox](https://github.com/moloch--/RootTheBox)
4. [PwningJuiceShop#HostingCTF](https://pwning.owasp-juice.shop/part1/ctf.html)
### Scoring server
1. Sette opp RootTheBox (scoring server). Dette kjører nåværende på systek-ctf-dashboard.herokuapp.com
2. Generere en xml med challenges for juice shop via `juice-shop-ctf-cli`. Det viktige å huske på her er at
nøkkelen man bruker for flaggene må samsvare med nøkkelen som er configurert i multi-juicer.
   
### Sette opp enkeltdeployments for hver deltaker.
1. Lag nytt cluster i digital ocean, hvis du ikke lar nåværende cluster leve frem til arrangementet. 
    1. `doctl kubernetes cluster create juicy-k8s`
    2. `helm repo add multi-juicer https://iteratec.github.io/multi-juicer/`
    3. stå i dette repoet og utfør `helm install -f values.yml multi-juicer ../multi-juicer/helm/multi-juicer/` for å 
få med korrekt configurasjon. All config kan endres her i `values.yml`
    4. Log inn som admin
    5. Hente admin secret `kubectl get secrets juice-balancer-secret -o=jsonpath='{.data.adminPassword}' | base64 --decode`
    6. Sette opp brukerkontoer for deltakerene?
    7. Sett opp en loadbalancer med tilgang til omverdenen. 
        1. `doctl compute certificate list` gir deg id til ditt cert (opprett i do GUI for ditt domene.)
        2. `wget https://raw.githubusercontent.com/iteratec/multi-juicer/master/guides/digital-ocean/do-lb.yaml`
        3. `vim do-lb.yaml`
        4. `kubectl create -f do-lb.yaml` (dette steget kan ta litt tid)
    8. Multijuicer er tilgjengelig på `systek-ctf.com` (ikke `www.systek-ctf.com`)
    
