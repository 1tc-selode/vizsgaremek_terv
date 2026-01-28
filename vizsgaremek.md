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

## Adatbázis Struktúra (MySQL)

### users (Felhasználók)
| Mező | Típus | Leírás |
|------|------|----------|
| id | INT (PK) | Egyedi azonosító |
| name | VARCHAR(255) | Felhasználó neve |
| email | VARCHAR(255) | E-mail cím (egyedi) |
| password | VARCHAR(255) | Titkosított jelszó |
| role | ENUM('user', 'admin') | Szerepkör |
| created_at | TIMESTAMP | Létrehozás dátuma |
| updated_at | TIMESTAMP | Módosítás dátuma |

### categories (Kategóriák)
| Mező | Típus | Leírás |
|------|------|----------|
| id | INT (PK) | Egyedi azonosító |
| name | VARCHAR(100) | Kategória neve |
| description | TEXT | Kategória leírása |
| created_at | TIMESTAMP | Létrehozás dátuma |
| updated_at | TIMESTAMP | Módosítás dátuma |

### reports (Bejelentések)
| Mező | Típus | Leírás |
|------|------|----------|
| id | INT (PK) | Egyedi azonosító |
| user_id | INT (FK) | Felhasználó azonosító |
| category_id | INT (FK) | Kategória azonosító |
| title | VARCHAR(255) | Bejelentés címe |
| description | TEXT | Részletes leírás |
| latitude | DECIMAL(10, 8) | Földrajzi szélesség |
| longitude | DECIMAL(11, 8) | Földrajzi hosszúság |
| date | DATETIME | Esemény dátuma |
| witnesses | INT | Tanúk száma |
| status | ENUM('pending', 'approved', 'rejected') | Állapot |
| created_at | TIMESTAMP | Létrehozás dátuma |
| updated_at | TIMESTAMP | Módosítás dátuma |

### report_images (Képek)
| Mező | Típus | Leírás |
|------|------|----------|
| id | INT (PK) | Egyedi azonosító |
| report_id | INT (FK) | Bejelentés azonosító |
| image_path | VARCHAR(255) | Kép elérési úja |
| created_at | TIMESTAMP | Feltöltés dátuma |

### votes (Hitelességi szavazatok)
| Mező | Típus | Leírás |
|------|------|----------|
| id | INT (PK) | Egyedi azonosító |
| report_id | INT (FK) | Bejelentés azonosító |
| user_id | INT (FK) | Felhasználó azonosító |
| vote_type | ENUM('credible', 'doubtful') | Szavazat típusa |
| created_at | TIMESTAMP | Szavazás dátuma |

### Relációk
- `users` 1:N `reports` - Egy felhasználó több bejelentést készíthet
- `categories` 1:N `reports` - Egy kategóriához több bejelentés tartozhat
- `reports` 1:N `report_images` - Egy bejelentéshez több kép tartozhat
- `reports` 1:N `votes` - Egy bejelentést több felhasználó értékelhet
- `users` 1:N `votes` - Egy felhasználó több szavazatot leadhat

## Főbb Funkciók

### 1. Bejelentési Rendszer (CRUD)
- **Bejelentés létrehozása**: Felhasználók új UFO vagy paranormális észlelést jelenthetnek
  - Dátum és időpont
  - **Helyszín (interaktív térkép)**:
    - Google Maps integráció a form-ban
    - Felhasználó rákattint a térképen az esemény helyszínére
    - Koordináták automatikusan kitöltődnek az input mezőbe
    - Marker megjelenítése a kiválasztott ponton
  - Jelenség típusa (UFO, szellem, kriptid, stb.)
  - Részletes leírás
  - Fotó feltöltés
  - Tanúk száma
  
- **Bejelentések listázása**: Szűrhető és kereshető lista
  - Kategória szerint
  - Helyszín szerint
  - Időpont szerint
  - Hitelesség szerint

- **Bejelentés megtekintése**: Részletes információk egyedi bejelentésről
  - Minden adat megjelenítése
  - **Helyszín térképen**: Google Maps beágyazva a bejelentés pontjával
  - Koordináta szöveg helyett interaktív térkép látszik
  - Marker a pontos helyszínen
  - Képgaléria
  
- **Bejelentés módosítása**: 
  - User: Saját bejelentések szerkesztése
  - Admin: Bármely bejelentés szerkesztése

- **Bejelentés törlése**: 
  - User: Saját bejelentések eltávolítása
  - Admin: Bármely bejelentés eltávolítása

### 2. Interaktív Térkép (Google Maps API)
- **Többszörös felhasználás**:
  - Bejelentés létrehozása (helyszín kiválasztása kattintással)
  - Bejelentés részletek (egyedi pont megjelenítése)
  - Összes bejelentés megjelenítése (lista nézet)
- Bejelentések megjelenítése térképen
- Google Maps integráció
- Egyedi marker-ek a bejelentésekhez
- Klaszterezés nagy mennyiségű adat esetén (Marker Clustering)
- Szűrés típus, időszak szerint
- Kattintható marker-ek részletes információkkal
- "Hotspot" területek kiemelése (sok bejelentés egy helyen)

### 3. Fotó Galéria
- Képek feltöltése bejelentésekhez
- Több kép támogatása bejelenésenként
- Miniatűr nézet és teljes méret
- Képoptimalizálás (backend)

### 4. Hitelességi Pont Rendszer
- Felhasználók értékelhetik mások bejelentéseit
- "Hiteles" vagy "Kétséges" szavazatok
- Hitelességi pontszám számítása
- Felhasználói reputáció

### 5. Felhasználói Rendszer
- Regisztráció, bejelentkezés, kijelentkezés
- Profil kezelés
- Saját bejelentések listázása
- Aktivitási előzmények
- Felhasználói statisztikák (bejelentések száma, reputáció)

### 6. Admin Funkciók
- **Admin Dashboard**: Áttekintés a rendszer állapotáról
- **Moderáció**: Bejelentések jóváhagyása/elutasítása
- **Felhasználók kezelése**: Felhasználók listázása, tiltása
- **Tartalom törlés**: Bármely bejelentés törlése
- **Kategóriák kezelése**: Új kategóriák létrehozása, módosítása, törlése
- **Képek kezelése**: Nem megfelelő képek törlése
- **Statisztikák megtekintése**: Részletes riportok

### 7. Statisztikák és Dashboard
- Bejelentések száma kategóriánként
- Havi/éves trendek
- Legnépszerűbb helyszínek
- Legaktívabb felhasználók

## API Végpontok (Backend - Laravel)

### Bejelentések
- `GET /api/reports` - Összes bejelentés listázása (szűrés, rendezés)
- `GET /api/reports/{id}` - Egy bejelentés részletei
- `POST /api/reports` - Új bejelentés létrehozása
- `PUT /api/reports/{id}` - Bejelentés módosítása
- `DELETE /api/reports/{id}` - Bejelentés törlése

### Térkép
- `GET /api/map/reports` - Bejelentések térképes megjelenítéshez
- `GET /api/map/hotspots` - Hotspot területek

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
- `GET /api/statistics/trends` - Trend adatok

## Egyedi Jellemzők

- **Interaktív koordináta választás**: Térképre kattintva automatikus kitöltés
- **Beágyazott térképek**: Koordináta szöveg helyett vizuális térkép
- **Kategorizálás**: Részletes jelenség típusok
- **Gamifikáció**: Jelvények, szintek a felhasználóknak
- **Modern dizájn**: Felhasználóbarát felület

## Projekt Előnyei Vizsgán

**Komplex CRUD műveletek** - Teljes értékű adatkezelés  
**REST API** - Backend-Frontend szétválasztás  
**Adatvalidáció** - Backend és Frontend szinten  
**Autentikáció & Authorizáció** - Laravel Sanctum Bearer token, szerepkör-alapú hozzáférés  
**Register/Login/Logout** - Teljes felhasználói életciklus  
**Admin felület** - Moderációs és kezelési funkciók  
**Fájl kezelés** - Kép feltöltés és tárolás  
**Google Maps API** - Térkép integráció, Geocoding, Marker Clustering  
**MySQL adatbázis** - Relációs adatbázis tervezés és kezelés  
**Responsive dizájn** - Mobilbarát felület  
**Adatbázis kapcsolatok** - Relációk kezelése (1:N, N:M)  
**Biztonsági szempontok** - SQL injection védelem, XSS védelem, szerepkör-alapú jogosultságok  

## Fejlesztési Fázisok

1. **Tervezés**: Adatbázis séma, API tervezés, mockupok
2. **Backend**: Laravel API végpontok, modellek, validáció
3. **Frontend**: Angular komponensek, service-ek, routing
4. **Integráció**: API összekapcsolás, tesztelés
5. **Finomhangolás**: Térkép, képek, hitelesség rendszer
6. **Dokumentáció**: Technikai dokumentáció, felhasználói útmutató
