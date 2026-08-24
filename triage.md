# Triage

**triage** er processen hvormed problemer undersøges for at beslutte hvilke er de mest seriøse og skal håndteres først.

Dette dokument beskriver vores tilgang til sortering og håndtering af feedback givet i repo'et.

## Kritiske fejl

Visse fejl er kritiske og eskaleres ASAP. Nedenfor beskrevne proces kan evt. omgås ved aftale i core-teamet, for hurtigere at få udgivet rettelser.

Med kritiske fejl menes:

- Fejl der forårsager nedetid
- Fejl der medfører sikkerhedsbrister

Hvis en kritisk fejl kan mitigeres ved kommunikation og procesændringer prioriteres dette først, hvorefter tekniske løsninger igangsættes.

## Out of scope

Visse typer feedback er out of scope for dette repo. Følg anvisningerne i [README.md](README.md).

Disse typer af feedback håndteres ikke i dette repo:

- **Spørgsmål og afklaringer om brug af platformen.** Disse henvises til de relevante kanaler i [OS2samtale](https://os2samtale.os2.eu/)

- **Ønsker til support for flere sprogmodeller.** Disse udvikles i samarbejde mellem de deltagende kommuner selv, samt eventuelt infrastrukturleverandører. Vi håber selvfølgelig at gode konfigurationer vil tilbydes tilbage til fællesskabet. Sådanne hører hjemme direkte i [helm-deployments repo'et som Pull Requests der tilføjer til model-listen](https://github.com/os2ai/helm-deployments/blob/0.6.0/applications/litellm/litellm-values.yaml#L92-L106)

## Afklaring og scoping

Vi forsøger som udgangspunkt at stille spørgsmål og finde mere information hvor det lader sig gøre over skrift.

Hvis feedback tydeliggør at core-teamet har behov for mere viden indenfor områder der vedrører kommunerne, sætter vi møder op med kommuner der indvilligere, for at opbygge en dybere forståelse.

Vi fanger og forklarer vores refleksioner og proces på de relevante issues, så man som feedback-bidragyder løbende får information om fremgangen på ens issue.

Hvis feedback ikke vil føre til umiddelbar teknisk implementering eller dokmentation, lukkes issuet med forklaring.

## Teknisk implementering

Hvis feedback givet som issue på dette repo vurderes at skulle føre til ny dokumentation eller teknisk implementering, åbnes et issue på et af de relevante repositories i [OS2ai-organisationen](https://github.com/os2ai).

Der linkes fra det nye issue tilbage til det oprindelige issue i dette repo, så forbindelsen fremgår.

Når den tekniske implementering eller opdaterede dokumentation er udført, lukkes issuet i det specifikke repository, og issuet i dette repo også.
