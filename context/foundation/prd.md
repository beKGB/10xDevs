---
project: "Strona Parafii"
version: 1
status: draft
created: 2026-09-21
context_type: greenfield
product_type: web-app
target_scale:
  users: medium
timeline_budget:
  mvp_weeks: 10
  hard_deadline: null
  after_hours_only: true
---

## Vision & Problem Statement

Parafianin musi dziś załatwiać prywatne sprawy z księdzem wyłącznie osobiście, w ramach jego sztywnych godzin przyjęć parafian — co oznacza czekanie w kolejce, gdy jest więcej osób, niedogodność terminową oraz konieczność dojazdu autem do parafii. Nie istnieje dziś żaden kanał, który pozwoliłby przekazać sprawę księdzu bez fizycznej obecności.

## User & Persona

Parafianin — członek jednej, konkretnej parafii, który chce przekazać księdzu prywatną sprawę bez konieczności czekania w kolejce w godzinach przyjęć lub dojazdu do parafii specjalnie w tym celu.

## Success Criteria

### Primary

- Pełny przepływ działa: parafianin wysyła zgłoszenie (email, opcjonalnie imię i nazwisko, rodzaj sprawy, treść, opcjonalne oznaczenie „pilne”), potwierdza je kodem wysłanym e-mailem, zgłoszenie trafia do systemu i parafianin dostaje link do sprawdzania jego statusu. Ksiądz loguje się do panelu, widzi zgłoszenie (przy „pilnym” — dodatkowo dostaje e-mail) i może oznaczyć je jako obsłużone.

### Secondary

- Estetyczny, dopracowany wizualnie wygląd strony.

### Guardrails

- Treść prywatnej wiadomości parafianina nie jest widoczna dla nikogo poza księdzem.
- Strona działa poprawnie na telefonie (urządzenia mobilne).

## User Stories

### US-01: Parafianin wysyła prywatne zgłoszenie do księdza

- **Given** parafianin bez konta wchodzi na stronę parafii
- **When** wypełnia formularz zgłoszenia, wysyła go i potwierdza kodem otrzymanym e-mailem
- **Then** zgłoszenie trafia do panelu księdza, a parafianin otrzymuje link do sprawdzania jego statusu

#### Acceptance Criteria
- Zgłoszenie nie trafia do systemu bez poprawnie wpisanego kodu weryfikacyjnego z e-maila.
- Pole email jest wymagane; imię i nazwisko są opcjonalne.
- Parafianin musi wybrać rodzaj sprawy z listy kategorii.

## Functional Requirements

### Formularz i wysyłka zgłoszenia

- FR-001: Parafianin może otworzyć formularz zgłoszenia z widocznego linku tekstowego (i/lub ikony) na stronie głównej. Priority: must-have
  > Socratic: Counter-argument considered: "sama ikonka koperty może być za mało widoczna dla starszych/mniej technicznych parafian." Resolution: kept, but FR doprecyzowany — obok/zamiast ikony musi być wyraźny link tekstowy.
- FR-002: Parafianin może wypełnić formularz zgłoszenia (email wymagany, imię i nazwisko opcjonalne, rodzaj sprawy z listy, treść wiadomości, opcjonalne oznaczenie „pilne”). Priority: must-have
  > Socratic: No counter-argument considered; stands as written.

  Kategorie „rodzaju sprawy” (z podkategoriami):
  1. **Sprawy sakramentalne i duchowe** — Spowiedź święta (Sakrament Pokuty i Pojednania, w konfesjonale lub po wcześniejszym umówieniu na dłuższą rozmowę); Kierownictwo duchowe (regularne rozmowy pomagające w rozwoju wiary i rozwiązywaniu dylematów sumienia); Prośba o Sakrament Chorych (namaszczenie osoby starszej, ciężko chorej lub w zagrożeniu życia — wizyta domowa/szpitalna); Modlitwa lub błogosławieństwo (np. poświęcenie przedmiotów kultu religijnego, błogosławieństwo domu/kolęda).
  2. **Sprawy kancelaryjne (formalno-prawne)** — Intencje mszalne (zamówienie mszy świętej w konkretnej intencji); Formalności przedmałżeńskie (protokół przedślubny, dokumenty z USC, zapowiedzi); Zgłoszenie chrztu dziecka (akt urodzenia, wybór chrzestnych, spisanie aktu chrztu); Pogrzeb katolicki (zgłoszenie zgonu, ustalenie terminu mszy i pochówku, akt zgonu z USC); Wydawanie zaświadczeń (np. świadectwo chrztu, zaświadczenia dla chrzestnych/świadków bierzmowania).
  3. **Poradnictwo i rozmowy wspierające** — Rozmowa w kryzysie małżeńskim lub rodzinnym; Wsparcie w żałobie po stracie bliskiej osoby; Poradnictwo bioetyczne lub moralne.
  4. **Sprawy administracyjne i społeczne** — Działalność w grupach parafialnych (oaza, caritas, chór, rada parafialna, ministranci); Sprawy charytatywne (zgłoszenie potrzeby pomocy materialnej lub chęć wsparcia takich akcji).
- FR-003: System wysyła parafianinowi e-mail z treścią zgłoszenia i kodem weryfikacyjnym po kliknięciu „wyślij”. Priority: must-have
  > Socratic: Counter-argument considered: "e-mail może trafić do spamu i zablokować zgłoszenie bez wyjaśnienia." Resolution: kept; ekran po wysłaniu formularza powinien przypominać o sprawdzeniu folderu spam.
- FR-004: Parafianin może wpisać kod weryfikacyjny na stronie, aby potwierdzić i faktycznie wysłać zgłoszenie. Priority: must-have
  > Socratic: Counter-argument considered: "kod bez limitu ważności to ryzyko bezpieczeństwa." Resolution: kept; wymóg ograniczonego czasu ważności kodu ujęty w wymaganiach niefunkcjonalnych.
- FR-005: System ogranicza liczbę zgłoszeń z tego samego źródła do maksymalnie 3 dziennie. Priority: must-have
  > Socratic: Counter-argument considered: "wspólne łącze (np. cała rodzina na jednym Wi-Fi) może zablokować kilku różnych parafian naraz." Resolution: kept jako świadomy kompromis — ochrona przed nadużyciami ważniejsza niż rzadki przypadek współdzielonego łącza; dokładny mechanizm identyfikacji źródła to decyzja techniczna na później.
- FR-006: Parafianin otrzymuje unikalny link do sprawdzania statusu swojego zgłoszenia. Priority: must-have
  > Socratic: Counter-argument considered: "jeśli ktoś przechwyci e-mail parafianina, zobaczy status jego prywatnej sprawy." Resolution: kept; wzmacnia to wymóg prywatności zapisany w Guardrails — link może pokazywać tylko status, nie treść zgłoszenia.

### Panel księdza

- FR-007: Ksiądz może zalogować się do panelu administracyjnego. Priority: must-have
  > Socratic: Counter-argument considered: "brak konta zapasowego to pojedynczy punkt awarii, jeśli ksiądz zapomni hasła." Resolution: kept; odzyskiwanie dostępu rozwiązane przez FR-013.
- FR-008: Ksiądz może przeglądać listę wszystkich zgłoszeń w panelu. Priority: must-have
  > Socratic: Counter-argument considered: "bez sortowania/filtrowania lista stanie się nieczytelna przy większej liczbie zgłoszeń." Resolution: uznane za trafne — patrz FR-011.
- FR-009: Ksiądz może oznaczyć zgłoszenie jako obsłużone. Priority: must-have
  > Socratic: Counter-argument considered: "samo oznaczenie bez notatki nie zostawia śladu, co ksiądz zrobił ze sprawą." Resolution: uznane za trafne — patrz FR-012.
- FR-010: System wysyła księdzu powiadomienie e-mail, gdy zgłoszenie jest oznaczone jako „pilne”. Priority: nice-to-have
  > Socratic: No counter-argument considered; stands as written.
- FR-011: Ksiądz może sortować/filtrować listę zgłoszeń w panelu (np. wg daty lub oznaczenia „pilne”). Priority: nice-to-have
- FR-012: Ksiądz może dodać krótką notatkę do zgłoszenia przy oznaczaniu go jako obsłużone. Priority: nice-to-have
- FR-013: Ksiądz może odzyskać dostęp do panelu poprzez link do nadania nowego hasła, wysyłany na jego e-mail. Priority: must-have

## Non-Functional Requirements

- Kod weryfikacyjny wysyłany e-mailem traci ważność po ograniczonym czasie i nie może zostać użyty bezterminowo.
- Treść prywatnego zgłoszenia parafianina nie jest widoczna dla nikogo poza księdzem, na żadnym etapie procesu.
- Strona przestrzega obowiązujących wymogów dot. plików cookie i prywatności użytkownika (np. zgoda na cookies, brak zbędnego śledzenia).

## Business Logic

System sprawdza treść zgłoszenia pod kątem wulgaryzmów i treści obraźliwych przed dopuszczeniem go do wysyłki, a przy oznaczeniu „pilne” dodatkowo ocenia, jak często dany parafianin ostatnio korzystał z tego oznaczenia, żeby ograniczyć nadużywanie priorytetu.

Reguła korzysta z dwóch danych wejściowych widocznych dla użytkownika: treści wiadomości wpisanej przez parafianina oraz historii niedawnych oznaczeń „pilne” z tego samego źródła zgłoszeń w ciągu dnia.

Wynikiem jest jedna z dwóch decyzji: albo zgłoszenie zostaje dopuszczone do dalszego procesu (wysyłka kodu weryfikacyjnego), albo zostaje odrzucone jako obraźliwe. Niezależnie od tego, czy zgłoszenie jest dopuszczone, oznaczenie „pilne” skutkuje dodatkowym powiadomieniem e-mail do księdza co najwyżej raz dziennie z danego źródła — kolejne tego dnia zgłoszenia oznaczone jako pilne nadal trafiają do panelu księdza, ale bez dodatkowego powiadomienia e-mail.

Parafianin spotyka się z tą regułą w dwóch miejscach: jeśli treść zostanie uznana za obraźliwą, dostaje e-mail informujący, że zgłoszenie jest obraźliwe i nie zostanie wysłane (zamiast kodu weryfikacyjnego). Ksiądz spotyka się z nią przez to, że dostaje najwyżej jedno powiadomienie e-mail o pilnej sprawie dziennie, nawet jeśli tego dnia oznaczono jako pilne więcej zgłoszeń.

## Access Control

- **Parafianin**: bez konta. Wysyła zgłoszenie przez formularz i otrzymuje unikalny link dostępowy / token, dzięki któremu może wrócić i sprawdzić status swojego zgłoszenia — bez zakładania konta ani hasła.
- **Ksiądz (administrator)**: osobne logowanie do panelu, w którym widzi wszystkie zgłoszenia od parafian i może oznaczać je jako obsłużone.

Model ról: płaski, dwuosobowy podział — parafianin (bez konta, dostęp przez token) i ksiądz/administrator (zalogowany) — bez dodatkowych ról pośrednich na tym etapie.

## Non-Goals

- **Obsługa wielu parafii naraz** — system obsługuje jedną, konkretną parafię i jednego księdza, nie platformę wieloparafialną.

## Open Questions

Brak otwartych pytań.
