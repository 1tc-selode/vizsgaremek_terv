# UFO / Paranormális Jelentő Rendszer - Vizsgaremek Terv

## Projekt Áttekintés

A vizsgaremek egy UFO és paranormális jelenségek bejelentő rendszere. Az applikáció célja, hogy a felhasználók könnyen jelenthessenek és böngészhessenek furcsa, megmagyarázhatatlan jelenségeket egy modern, felhasználóbarát felületen keresztül.

## Alkalmazás Célja

Olyan platform létrehozása, ahol a felhasználók:
- Bejelenthetik UFO-észleléseiket és paranormális élményeiket
- Térképen követhetik a bejelentéseket
- Fotókkal dokumentálhatják tapasztalataikat
- Értékelhetik mások bejelentéseinek hitelességét
- Statisztikákat láthatnak a jelenségekről

## Technológiai Stack

- **Frontend**: Angular (SPA - Single Page Application)
- **Backend**: Laravel (REST API)
- **Adatbázis**: MySQL
- **API**: RESTful API végpontok
- **Térkép**: Google Maps API
- **Authentikáció**: Laravel Sanctum (Bearer Token)

## Adatbázis Terv

**users:** id, name, email, password, role, created_at, updated_at, deleted_at

**categories:** id, name, description, created_at, updated_at, deleted_at

**reports:** id, user_id (FK), category_id (FK), title, description, latitude, longitude, date, witnesses, status, created_at, updated_at, deleted_at

**report_images:** id, report_id (FK), image_path, created_at, deleted_at

**votes:** id, report_id (FK), user_id (FK), vote_type, created_at, deleted_at

### Kapcsolatok:
- **User -> Reports:** 1:N (egy felhasználó több bejelentést hozhat létre)
- **Category -> Reports:** 1:N (egy kategóriához több bejelentés tartozhat)
- **Report -> Report Images:** 1:N (egy bejelentéshez több kép tartozhat)
- **Report -> Votes:** 1:N (egy bejelentést több felhasználó értékelhet)
- **User -> Votes:** 1:N (egy felhasználó több szavazatot leadhat)
- **User -> Report -> Votes:** N:M (many-to-many: felhasználók értékelik a bejelentéseket)
---

## Authentikáció és Szerepkörök

A rendszer két felhasználói szerepkört támogat:

### User (Felhasználó)
- Bejelentések létrehozása
- Saját bejelentések módosítása és törlése
- Képek feltöltése bejelentésekhez
- Mások bejelentéseinek értékelése
- Bejelentések böngészése és megtekintése

### Admin (Adminisztrátor)
- Minden User jogosultság
- **Bármely bejelentés módosítása és törlése**
- Bejelentések moderálása (jóváhagyás/elutasítás)
- Felhasználók kezelése és tiltása
- Kategóriák kezelése (létrehozás, módosítás, törlés)
- Bármely kép törlése
- Rendszer statisztikák megtekintése

### Authentikáció Működése
- **Laravel Sanctum** Bearer token alapú authentikáció
- **Regisztráció**: Új felhasználók automatikusan "User" szerepkört kapnak
- **Login**: Bearer token generálása sikeres bejelentkezés után
- **Logout**: Token érvénytelenítése
- **Védett végpontok**: `Authorization: Bearer {token}` header szükséges
- **Szerepkör ellenőrzés**: Middleware-ek biztosítják a jogosultság-alapú hozzáférést

## Főbb Funkciók

### 1. Bejelentési Rendszer (CRUD)

**Home (Főoldal) - Lista Nézet**
- Reddit-szerű kártyák: cím, kategória, dátum, hitelesség pontszám
- Fel/Le szavazás minden bejelentésnél
- Módosítás/Törlés gombok (User: saját, Admin: minden)
- Szűrés: kategória, dátum, hitelesség

**Részletek Oldal**
- Teljes bejelentés: leírás, Google Maps térkép, képgaléria
- Fel/Le szavazás
- Módosítás/Törlés (User: saját, Admin: minden)

**Új/Módosítás Oldal**
- Form: cím, kategória, leírás, dátum, interaktív Google Maps, tanúk száma, képek
- Mentés/Mégse gombok

### 2. Hitelességi Értékelés
- Fel/Le szavazás (lista és részletek oldalon)
- Pontszám számítás: fel - le szavazatok
- Egy user egyszer szavazhat bejelenésenként

### 3. Statisztikák
- Összes bejelentés eddig
- Leghitelesebb bejelentés (legmagasabb pontszám)
- Leg el nem hihető bejelentés (legalacsonyabb pontszám)

### 4. Admin Funkciók
- Dashboard: rendszer áttekintés
- Bármely bejelentés módosítása/törlése
- Felhasználók kezelése, tiltás
- Kategóriák kezelése (CRUD)
- Képek moderálása

## API Végpontok (Backend - Laravel)

### Bejelentések
- `GET /api/reports` - Összes bejelentés listázása (szűrés, rendezés)
- `GET /api/reports/{id}` - Egy bejelentés részletei
- `POST /api/reports` - Új bejelentés létrehozása
- `PUT /api/reports/{id}` - Bejelentés módosítása
- `DELETE /api/reports/{id}` - Bejelentés törlése

### Térkép
- `GET /api/map/reports` - Bejelentések térképes megjelenítéshez

### Képek
- `POST /api/reports/{id}/images` - Kép feltöltése bejelentéshez
- `GET /api/reports/{id}/images` - Bejelentés képeinek listázása
- `DELETE /api/images/{id}` - Kép törlése (saját)

### Kategóriák
- `GET /api/categories` - Összes kategória listázása
- `GET /api/categories/{id}` - Egy kategória részletei
- `POST /api/categories` - Új kategória létrehozása (csak Admin)
- `PUT /api/categories/{id}` - Kategória módosítása (csak Admin)
- `DELETE /api/categories/{id}` - Kategória törlése (csak Admin)

### Értékelések
- `POST /api/reports/{id}/vote` - Szavazás leadása
- `GET /api/reports/{id}/credibility` - Hitelessági pontszám

### Authentikáció
- `POST /api/register` - Regisztráció
- `POST /api/login` - Bejelentkezés (Bearer token visszaadása)
- `POST /api/logout` - Kijelentkezés (token érvénytelenítése)
- `GET /api/user` - Bejelentkezett felhasználó adatai

**Megjegyzés**: Minden védett végpont Bearer token autentikációt igényel a `Authorization: Bearer {token}` header-ben.

### Felhasználók
- `GET /api/profile` - Profil adatok
- `PUT /api/profile` - Profil módosítás
- `GET /api/users/{id}/reports` - Felhasználó bejelentései

### Admin Végpontok (csak Admin szerepkörrel)
- `GET /api/admin/reports` - Összes bejelentés kezelése
- `DELETE /api/admin/reports/{id}` - Bármely bejelentés törlése
- `PUT /api/admin/reports/{id}/approve` - Bejelentés jóváhagyása
- `GET /api/admin/users` - Összes felhasználó listázása
- `PUT /api/admin/users/{id}/ban` - Felhasználó tiltása
- `GET /api/admin/statistics` - Admin statisztikák

### Statisztikák
- `GET /api/statistics` - Általános statisztikák

## Fejlesztési Fázisok

1. **Tervezés**: Adatbázis séma, API tervezés, mockupok
2. **Backend**: Laravel API végpontok, modellek, validáció
3. **Frontend**: Angular komponensek, service-ek, routing
4. **Integráció**: API összekapcsolás, tesztelés
5. **Finomhangolás**: Térkép, képek, hitelesség rendszer
6. **Dokumentáció**: Technikai dokumentáció, felhasználói útmutató
