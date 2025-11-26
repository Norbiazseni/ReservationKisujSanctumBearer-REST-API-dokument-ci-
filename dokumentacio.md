# ReservationKisújBearer REST API Dokumentáció

A dokumentáció a **fakestoreapi.com** mintájára készült.

---

## Alapinformációk

**Base URL:** `http://127.0.0.1/reservationKisujSanctumBearer/public/api`

Az API autentikációhoz **Bearer Token** szükséges. A tokent a `/login` végpont adja vissza.

---

## Végpontok:

Érvénytelen vagy hiányzó token esetén a backendnek a következő hibát kell adnia:

```json
Response: 401 Unauthorized
{
  "message": "Invalid token"
}
```

### Nem védett végpontok:
- **GET** `/hello` - teszteléshez
- **POST** `/register` - regisztrációhoz
- **POST** `/login` - belépéshez

### Hibák:
- 400 Bad Request: Hibás formátum
- 401 Unauthorized: Nincs jogosultság. 
- 403 Forbidden: A felhasználó nem jogosult a kérés végrehajtására. 
- 404 Not Found: Nem található a kérés.
- 503 Service Unavailable: Váratlan hiba, nem elérhető.

---



# 🔐 Autentikációs Végpontok

## POST /register

**Új felhasználó létrehozása**

**Fejlécek:**

```
Content-Type: application/json
Accept: application/json
```

**Body:**

```json
{
  "name": "Teszt Elek",
  "email": "teszt@example.com",
  "password": "Jelszo_2025",
  "password_confirmation": "Jelszo_2025"
}
```

**Válasz (siker):** – A szerver visszaadja a létrehozott felhasználót.

```json
{
  "message": "User registered successfully",
  "user": {
    "name": "Teszt Elek",
    "email": "teszt@example.com",
    "updated_at": "2025-11-19T16:43:32.000001",
    "created_at": "2025-11-21T17:24:12.000004"
  }
}
```

---

## POST /login

**Bejelentkezés és Bearer Token generálása**

**Fejlécek:**

```
Content-Type: application/json
Accept: application/json
```

**Body:**

```json
{
  "email": "sipnor@gmail.com",
  "password": "Jelszo_2025"
}
```

**Válasz (siker):**

```json
{
  "access_token": "1|kcmV2i8n9xbQBoVY0LqQCvs4L5OvkK0pXf3UixEaa5bb606f",
  "token_type": "Bearer"
}
```

---

## POST /logout

**Kijelentkezés – a token érvénytelenítése**

**Fejlécek:**

```
Content-Type: application/json
Accept: application/json
Authorization: Bearer 5|vrKwcP2klx42uC4svjk8gDVKIPa2a74AALzxVzzn538f7ea2
```

**Válasz:**

```
"Sikeres kijelentkezés"
```

---

# 📅 Foglalások (Reservations)

Az alábbi végpontok **auth:sanctum** middleware védelemmel vannak ellátva, ezért Bearer Token szükséges.

Admin felhasználó: **mindent lát**.
Normál user: **csak a saját foglalásait látja / módosíthatja / törölheti**.

---

## GET /reservations

**Összes foglalás lekérése**

Admin: minden foglalást visszaad.
User: csak a saját foglalásait.

**Fejlécek:**

```
Content-Type: application/json
Accept: application/json
Authorization: Bearer 5|vrKwcP2klx42uC4svjk8gDVKIPa2a74AALzxVzzn538f7ea2

```

**Válasz (példa):**

```json
[
  {
    "id": 1,
    "user_id": 13,
    "reservation_time": "2025-02-14 18:00:00",
    "guests": 4,
    "note": "szülinap"
  }
]
```

---

## GET /reservations/{id}

**Egy adott foglalás lekérése**

Ha a foglalás nem a userhez tartozik vagy nem admin → **403 Forbidden**.

**Válasz (siker):**

```json
{
  "id": 1,
  "user_id": 13,
  "reservation_time": "2025-02-14 18:00:00",
  "guests": 4,
  "note": "szülinap"
}
```

**Válasz (nem jogosult):**

```json
{
  "message": "Unauthorized"
}
```

---

## POST /reservations

**Új foglalás létrehozása**

**Kötelező mezők:**

* reservation_time (date)
* guests (integer, min 1)
* note (opcionális)

**Fejlécek:**

* Content-Type: application/json
* Accept: application/json

**Kérés (példa):**

```json
{
	"name": "Pista",
    "email": "pista@gmail.com",
    "reservation_time": "2025-12-20 10:48:00",
    "guests": "5",
    "note": "Depresszios vacsi"
}
```

**Válasz:**

```json
{
    "id": 42,
    "name": "Pista",
    "email": "pista@gmail.com",
    "reservation_time": "2025-12-20 10:48:00",
    "guests": 5,
    "note": "Depresszios vacsi",
    "created_at": "2025-11-26 10:52:17",
    "updated_at": "2025-11-26 10:52:17"
}
```

---

## PUT /reservations/{id}

**Foglalás teljes módosítása**

Foglalás teljes módosítása

**Válasz:**

```json
{
  "id": 5,
  "user_id": 13,
  "reservation_time": "2025-03-11 14:00:00",
  "guests": 3,
  "note": "új időpont"
}
```

---

## PATCH /reservations/{id}

**Foglalás részleges módosítása**

**Kérés (példa):**

```json
{
  "guests": 5
}
```

**Válasz:**

```json
{
  "id": 5,
  "user_id": 13,
  "reservation_time": "2025-03-11 14:00:00",
  "guests": 5,
  "note": "új időpont"
}
```

---

## DELETE /reservations/{id}

**Foglalás törlése**

User csak a sajátját törölheti.

**Válasz:**

```json
{
  "message": "Foglalás törölve."
}
```

---

# ✔️ Jogosultsági összefoglaló

| Művelet                   | User | Admin |
| ------------------------- | ---- | ----- |
| Saját foglalás listázása  | ✔️   | ✔️    |
| Minden foglalás listázása | ❌    | ✔️    |
| Foglalás létrehozása      | ✔️   | ✔️    |
| Saját foglalás módosítása | ✔️   | ✔️    |
| Más foglalás módosítása   | ❌    | ✔️    |
| Saját foglalás törlése    | ✔️   | ✔️    |
| Más foglalás törlése      | ❌    | ✔️    |

---


