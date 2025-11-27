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
- 401 Unauthorized: Nem hitelesített, nincs jogosultság
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

**Fejlécek:**

```
Content-Type: application/json
Accept: application/json
Authorization: Bearer 5|vrKwcP2klx42uC4svjk8gDVKIPa2a74AALzxVzzn538f7ea2

```

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

**Fejlécek:**

```
Content-Type: application/json
Accept: application/json
Authorization: Bearer 5|vrKwcP2klx42uC4svjk8gDVKIPa2a74AALzxVzzn538f7ea2

```

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

**Fejlécek:**
```
Content-Type: application/json
Accept: application/json
Authorization: Bearer 5|vrKwcP2klx42uC4svjk8gDVKIPa2a74AALzxVzzn538f7ea2

```

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

**Fejlécek:**

```
Content-Type: application/json
Accept: application/json
Authorization: Bearer 5|vrKwcP2klx42uC4svjk8gDVKIPa2a74AALzxVzzn538f7ea2

```

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

# III. Modul Tesztelés 

Ez a feature teszt ideális HTTP kérések szimulálására, mivel több komponens (Controller, Middleware, Auth) együttműködését vizsgáljuk.

`reservationKisujBearer>php artisan make:test AuthControllerTest`

```php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;
use PHPUnit\Framework\Attributes\Test;
use App\Models\User;

class AuthControllerTest extends TestCase
{
    /**
     * Ezek a tesztek az alapvető autentikációs folyamatot ellenőrzik:
     * - User registration
     * - User login with token generation
     * - User logout via API token invalidation
     */

    use RefreshDatabase;

    /**
     * Teszt: A felhasználó sikeresen tud regisztrálni érvényes adatokkal.
     *
     * Ez a teszt elküld egy POST kérést a /api/register végpontra egy érvényes adatkészlettel, és ellenőrzi, hogy:
     * - A válaszkód 201 (Létrehozva)
     * - A JSON válasz tartalmazza a megfelelő szerkezetet
     * - A felhasználó e-mail címével bekerül az adatbázisba
     */
    #[Test]
    public function user_can_register()
    {
        // Arrange
        $payload = [
            'name' => 'Adrián',
            'email' => 'adrian@gmail.com',
            'password' => 'Jelszo_2025',
            'password_confiramtion' => 'Jelszo_2025'
        ];

        // Act
        $response = $this->postJson('/api/register', $payload);

        // Assert
        $response->assertStatus(201)->assertJsonStructure(['message', 'user']);
        $this->assertDatabaseHas('users', ['email' => 'adrian@gmail.com']);
    }

    /**
     * Teszt: A felhasználó be tud jelentkezni, és hozzáférési tokent kap.
     *
     * Ez a teszt ellenőrzi, hogy egy érvényes belépési adatokkal rendelkező felhasználó:
     * - Helyes válaszkódot kapjon (200)
     * - A válasz tartalmazza az access_token és token_type mezőket
     */
    #[Test]
    public function user_can_login_and_receive_token()
    {
        // Arrange
        $user = User::factory()->create([
            'email' => 'teszt@gmail.com',
            'password' => 'password'
        ]);

        $credentials = [
            'email' => 'teszt@gmail.com',
            'password' => 'password'
        ];

        // Act
        $response = $this->postJson('/api/login', $credentials);

        // Assert
        $response->assertStatus(200)->assertJsonStructure(['access_token', 'token_type']);
    }

    /**
     * Teszt: A felhasználó sikeresen ki tud jelentkezni.
     *
     * A teszt ellenőrzi, hogy:
     * - Egy érvényes tokennel rendelkező felhasználó sikeresen meghívhatja a /api/logout végpontot
     * - A válaszkód 200 legyen
     * - A visszakapott JSON üzenet jelzi a sikeres kijelentkezést endpoint returns a successful response
     * - The JSON message indicates successful logout
     */
    #[Test]
    public function user_can_logout()
    {
        // Arrange
        $user = User::factory()->create();
        $token = $user->createToken('auth_token')->plainTextToken;

        // Act
        $response = $this->withHeaders([
            'Authorization' => 'Bearer ' . $token,
        ])->postJson('/api/logout');

        // Assert
        $response->assertStatus(200)->assertJson([
            'message' => 'Logged out successfully',
        ]);
    }
}

```

`reservationKisujBearer>php artisan make:test ReservationAccesTest`
```php
<?php

namespace Tests\Feature;

use App\Models\User;
use App\Models\Reservation;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use PHPUnit\Framework\Attributes\Test;

class ReservationAccessTest extends TestCase
{
    use RefreshDatabase;

    /**
     * Teszt: Az admin képes megtekinteni az összes foglalást.
     *
     * Az admin felhasználó autentikálva lekéri a /api/reservations végpontot,
     * és ellenőrizzük, hogy a válasz tartalmazza a létrehozott foglalást.
     */
    #[Test]
    public function admin_can_view_all_reservations()
    {
        $admin = User::factory()->create(['is_admin' => true]);
        $user = User::factory()->create();
        $reservation = Reservation::factory()->for($user)->create();

        $response = $this->actingAs($admin)->getJson('/api/reservations');

        $response->assertStatus(200)
                 ->assertJsonFragment(['id' => $reservation->id]);
    }

    /**
     * Teszt: A felhasználó csak a saját foglalásait láthatja.
     */
    #[Test]
    public function user_can_view_own_reservations()
    {
        $user = User::factory()->create();
        $reservation = Reservation::factory()->for($user)->create();

        $response = $this->actingAs($user)->getJson('/api/reservations');

        $response->assertStatus(200)
                 ->assertJsonFragment(['id' => $reservation->id]);
    }

    /**
     * Teszt: A felhasználó nem láthatja más felhasználó foglalását.
     */
    #[Test]
    public function user_cannot_view_others_reservation()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $reservation = Reservation::factory()->for($otherUser)->create();

        $response = $this->actingAs($user)->getJson("/api/reservations/{$reservation->id}");

        $response->assertStatus(403);
    }

    /**
     * Teszt: A felhasználó frissítheti a saját foglalását.
     */
    #[Test]
    public function user_can_update_own_reservation()
    {
        $user = User::factory()->create();
        $reservation = Reservation::factory()->for($user)->create();

        $updateData = ['note' => 'Frissített megjegyzés'];

        $response = $this->actingAs($user)->putJson("/api/reservations/{$reservation->id}", $updateData);

        $response->assertStatus(200)
                 ->assertJsonFragment(['note' => 'Frissített megjegyzés']);
    }

    /**
     * Teszt: A felhasználó nem frissítheti más foglalását.
     */
    #[Test]
    public function user_cannot_update_others_reservation()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $reservation = Reservation::factory()->for($otherUser)->create();

        $updateData = ['note' => 'Tiltott frissítés'];

        $response = $this->actingAs($user)->putJson("/api/reservations/{$reservation->id}", $updateData);

        $response->assertStatus(403);
    }

    /**
     * Teszt: A felhasználó törölheti a saját foglalását.
     */
    #[Test]
    public function user_can_delete_own_reservation()
    {
        $user = User::factory()->create();
        $reservation = Reservation::factory()->for($user)->create();

        $response = $this->actingAs($user)->deleteJson("/api/reservations/{$reservation->id}");

        $response->assertStatus(200)
                 ->assertJson(['message' => 'Foglalás törölve.']);
    }

    /**
     * Teszt: A felhasználó nem törölheti más felhasználó foglalását.
     */
    #[Test]
    public function user_cannot_delete_others_reservation()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $reservation = Reservation::factory()->for($otherUser)->create();

        $response = $this->actingAs($user)->deleteJson("/api/reservations/{$reservation->id}");

        $response->assertStatus(403);
    }
}

```

`reservationKisujBearer>php artisan make:test ReservationControllerTest`

```php
<?php

namespace Tests\Feature;

use App\Models\User;
use App\Models\Reservation;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use PHPUnit\Framework\Attributes\Test;
use Illuminate\Foundation\Testing\WithFaker;

class ReservationControllerTest extends TestCase
{
    use RefreshDatabase, WithFaker;

    /**
     * Teszt: A felhasználó létre tud hozni foglalást.
     * Ellenőrizzük, hogy:
     * - 201-es státuszkód érkezik
     * - A válasz JSON tartalmazza a foglalás adatait
     * - A foglalás bekerül az adatbázisba
     */
    #[Test]
    public function user_can_create_reservation()
    {
        $user = User::factory()->create();
        $payload = [
            'reservation_time' => now()->addDays(3)->toDateTimeString(),
            'guests' => 4,
            'note' => 'Teszt foglalás',
        ];

        $response = $this->actingAs($user)->postJson('/api/reservations', $payload);

        $response->assertStatus(201)
                 ->assertJsonFragment(['note' => 'Teszt foglalás']);
        $this->assertDatabaseHas('reservations', ['note' => 'Teszt foglalás']);
    }

    /**
     * Teszt: A felhasználó megtekintheti a saját foglalásait.
     */
    #[Test]
    public function user_can_view_own_reservations()
    {
        $user = User::factory()->create();
        $reservation = Reservation::factory()->for($user)->create();

        $response = $this->actingAs($user)->getJson('/api/reservations');

        $response->assertStatus(200)
                 ->assertJsonFragment(['id' => $reservation->id]);
    }

    /**
     * Teszt: A felhasználó nem láthatja más foglalását.
     */
    #[Test]
    public function user_cannot_view_others_reservation()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $reservation = Reservation::factory()->for($otherUser)->create();

        $response = $this->actingAs($user)->getJson("/api/reservations/{$reservation->id}");

        $response->assertStatus(403);
    }

    /**
     * Teszt: A felhasználó frissítheti a saját foglalását.
     */
    #[Test]
    public function user_can_update_own_reservation()
    {
        $user = User::factory()->create();
        $reservation = Reservation::factory()->for($user)->create();

        $updateData = ['note' => 'Frissített megjegyzés'];

        $response = $this->actingAs($user)->putJson("/api/reservations/{$reservation->id}", $updateData);

        $response->assertStatus(200)
                 ->assertJsonFragment(['note' => 'Frissített megjegyzés']);
        $this->assertDatabaseHas('reservations', ['note' => 'Frissített megjegyzés']);
    }

    /**
     * Teszt: A felhasználó nem módosíthatja más foglalását.
     */
    #[Test]
    public function user_cannot_update_others_reservation()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $reservation = Reservation::factory()->for($otherUser)->create();

        $updateData = ['note' => 'Tiltott frissítés'];

        $response = $this->actingAs($user)->putJson("/api/reservations/{$reservation->id}", $updateData);

        $response->assertStatus(403);
    }

    /**
     * Teszt: A felhasználó törölheti a saját foglalását.
     */
    #[Test]
    public function user_can_delete_own_reservation()
    {
        $user = User::factory()->create();
        $reservation = Reservation::factory()->for($user)->create();

        $response = $this->actingAs($user)->deleteJson("/api/reservations/{$reservation->id}");

        $response->assertStatus(200)
                 ->assertJson(['message' => 'Foglalás törölve.']);
        $this->assertDatabaseMissing('reservations', ['id' => $reservation->id]);
    }

    /**
     * Teszt: A felhasználó nem törölheti más foglalását.
     */
    #[Test]
    public function user_cannot_delete_others_reservation()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $reservation = Reservation::factory()->for($otherUser)->create();

        $response = $this->actingAs($user)->deleteJson("/api/reservations/{$reservation->id}");

        $response->assertStatus(403);
    }

    /**
     * Teszt: Az admin minden foglalást láthat.
     */
    #[Test]
    public function admin_can_view_all_reservations()
    {
        $user = User::factory()->create();
        $admin = User::factory()->create(['is_admin' => true]);
        $reservation = Reservation::factory()->for($user)->create();

        $response = $this->actingAs($admin)->getJson('/api/reservations');

        $response->assertStatus(200)
                 ->assertJsonFragment(['id' => $reservation->id]);
    }
}
```

`reservationKisujBearer>php artisan test`
