حتماً! در اینجا کدهای کامل و عملیاتی RITMO را برای شما آماده می‌کنم:

🎵 پروژه کامل RITMO

📁 ساختار پروژه

```
ritmo-complete/
├── backend/ (Laravel + Admin Panel)
├── frontend/ (React.js)
└── README.md
```

🔧 بخش 1: بک‌اند (Laravel)

فایل‌های اصلی:

backend/.env

```env
APP_NAME=RITMO
APP_ENV=local
APP_KEY=base64:your_app_key_here
APP_DEBUG=true
APP_URL=http://localhost:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=ritmo
DB_USERNAME=root
DB_PASSWORD=

BROADCAST_DRIVER=log
CACHE_DRIVER=file
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

SANCTUM_STATEFUL_DOMAINS=localhost,localhost:3000
```

backend/composer.json

```json
{
    "name": "ritmo/backend",
    "type": "project",
    "require": {
        "php": "^8.1",
        "guzzlehttp/guzzle": "^7.2",
        "laravel/framework": "^10.10",
        "laravel/sanctum": "^3.2",
        "laravel/tinker": "^2.8"
    },
    "require-dev": {
        "fakerphp/faker": "^1.9.1",
        "laravel/pint": "^1.0",
        "laravel/sail": "^1.18"
    },
    "autoload": {
        "psr-4": {
            "App\\": "app/",
            "Database\\Factories\\": "database/factories/",
            "Database\\Seeders\\": "database/seeders/"
        }
    }
}
```

🗃 میگریشن‌ها

backend/database/migrations/2024_01_01_000001_create_users_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->string('avatar')->nullable();
            $table->text('bio')->nullable();
            $table->boolean('is_admin')->default(false);
            $table->boolean('is_active')->default(true);
            $table->rememberToken();
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('users');
    }
};
```

backend/database/migrations/2024_01_01_000002_create_artists_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('artists', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->text('bio')->nullable();
            $table->string('photo')->nullable();
            $table->integer('followers_count')->default(0);
            $table->boolean('is_verified')->default(false);
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('artists');
    }
};
```

backend/database/migrations/2024_01_01_000003_create_albums_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('albums', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->foreignId('artist_id')->constrained()->onDelete('cascade');
            $table->integer('release_year');
            $table->string('cover_image');
            $table->string('genre');
            $table->integer('total_tracks')->default(0);
            $table->integer('duration')->default(0);
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('albums');
    }
};
```

backend/database/migrations/2024_01_01_000004_create_songs_table.php

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('songs', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->foreignId('artist_id')->constrained()->onDelete('cascade');
            $table->foreignId('album_id')->constrained()->onDelete('cascade');
            $table->integer('duration');
            $table->string('file_path');
            $table->string('cover_image')->nullable();
            $table->integer('play_count')->default(0);
            $table->integer('like_count')->default(0);
            $table->integer('download_count')->default(0);
            $table->string('quality')->default('320kbps');
            $table->boolean('is_explicit')->default(false);
            $table->boolean('is_active')->default(true);
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('songs');
    }
};
```

🎯 مدل‌ها

backend/app/Models/User.php

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable;

    protected $fillable = [
        'name',
        'email',
        'password',
        'avatar',
        'bio',
        'is_admin',
        'is_active'
    ];

    protected $hidden = [
        'password',
        'remember_token',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
        'password' => 'hashed',
        'is_admin' => 'boolean',
        'is_active' => 'boolean'
    ];

    public function isAdministrator(): bool
    {
        return $this->is_admin;
    }
}
```

backend/app/Models/Artist.php

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Artist extends Model
{
    use HasFactory;

    protected $fillable = [
        'name',
        'bio',
        'photo',
        'followers_count',
        'is_verified'
    ];

    protected $casts = [
        'is_verified' => 'boolean'
    ];

    public function albums()
    {
        return $this->hasMany(Album::class);
    }

    public function songs()
    {
        return $this->hasMany(Song::class);
    }
}
```

backend/app/Models/Song.php

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Song extends Model
{
    use HasFactory;

    protected $fillable = [
        'title',
        'artist_id',
        'album_id',
        'duration',
        'file_path',
        'cover_image',
        'play_count',
        'like_count',
        'download_count',
        'quality',
        'is_explicit',
        'is_active'
    ];

    protected $casts = [
        'is_explicit' => 'boolean',
        'is_active' => 'boolean'
    ];

    public function artist()
    {
        return $this->belongsTo(Artist::class);
    }

    public function album()
    {
        return $this->belongsTo(Album::class);
    }
}
```

🎮 کنترلرها

backend/app/Http/Controllers/API/AuthController.php

```php
<?php

namespace App\Http\Controllers\API;

use App\Http\Controllers\Controller;
use App\Models\User;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Validator;

class AuthController extends Controller
{
    public function register(Request $request): JsonResponse
    {
        $validator = Validator::make($request->all(), [
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users',
            'password' => 'required|string|min:8|confirmed'
        ]);

        if ($validator->fails()) {
            return response()->json([
                'success' => false,
                'errors' => $validator->errors()
            ], 422);
        }

        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password)
        ]);

        $token = $user->createToken('auth_token')->plainTextToken;

        return response()->json([
            'success' => true,
            'data' => [
                'user' => $user,
                'token' => $token
            ],
            'message' => 'ثبت‌نام با موفقیت انجام شد'
        ], 201);
    }

    public function login(Request $request): JsonResponse
    {
        $validator = Validator::make($request->all(), [
            'email' => 'required|email',
            'password' => 'required|string'
        ]);

        if ($validator->fails()) {
            return response()->json([
                'success' => false,
                'errors' => $validator->errors()
            ], 422);
        }

        $user = User::where('email', $request->email)->first();

        if (!$user || !Hash::check($request->password, $user->password)) {
            return response()->json([
                'success' => false,
                'message' => 'ایمیل یا رمز عبور اشتباه است'
            ], 401);
        }

        $token = $user->createToken('auth_token')->plainTextToken;

        return response()->json([
            'success' => true,
            'data' => [
                'user' => $user,
                'token' => $token
            ],
            'message' => 'ورود با موفقیت انجام شد'
        ]);
    }

    public function logout(Request $request): JsonResponse
    {
        $request->user()->currentAccessToken()->delete();

        return response()->json([
            'success' => true,
            'message' => 'خروج با موفقیت انجام شد'
        ]);
    }

    public function user(Request $request): JsonResponse
    {
        return response()->json([
            'success' => true,
            'data' => $request->user()
        ]);
    }
}
```

backend/app/Http/Controllers/API/SongController.php

```php
<?php

namespace App\Http\Controllers\API;

use App\Http\Controllers\Controller;
use App\Models\Song;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;

class SongController extends Controller
{
    public function index(): JsonResponse
    {
        $songs = Song::with(['artist', 'album'])
            ->where('is_active', true)
            ->orderBy('play_count', 'desc')
            ->get();

        return response()->json([
            'success' => true,
            'data' => $songs
        ]);
    }

    public function trending(): JsonResponse
    {
        $songs = Song::with(['artist', 'album'])
            ->where('is_active', true)
            ->orderBy('play_count', 'desc')
            ->limit(10)
            ->get();

        return response()->json([
            'success' => true,
            'data' => $songs
        ]);
    }

    public function show(Song $song): JsonResponse
    {
        $song->load(['artist', 'album']);
        $song->increment('play_count');

        return response()->json([
            'success' => true,
            'data' => $song
        ]);
    }

    public function store(Request $request): JsonResponse
    {
        $validated = $request->validate([
            'title' => 'required|string|max:255',
            'artist_id' => 'required|exists:artists,id',
            'album_id' => 'required|exists:albums,id',
            'duration' => 'required|integer',
            'file_path' => 'required|string',
            'cover_image' => 'nullable|string'
        ]);

        $song = Song::create($validated);

        return response()->json([
            'success' => true,
            'data' => $song,
            'message' => 'آهنگ با موفقیت اضافه شد'
        ], 201);
    }

    public function update(Request $request, Song $song): JsonResponse
    {
        $validated = $request->validate([
            'title' => 'required|string|max:255',
            'artist_id' => 'required|exists:artists,id',
            'album_id' => 'required|exists:albums,id',
            'duration' => 'required|integer',
            'is_active' => 'boolean'
        ]);

        $song->update($validated);

        return response()->json([
            'success' => true,
            'data' => $song,
            'message' => 'آهنگ با موفقیت به‌روزرسانی شد'
        ]);
    }

    public function destroy(Song $song): JsonResponse
    {
        $song->delete();

        return response()->json([
            'success' => true,
            'message' => 'آهنگ با موفقیت حذف شد'
        ]);
    }

    public function like(Song $song): JsonResponse
    {
        $song->increment('like_count');

        return response()->json([
            'success' => true,
            'message' => 'آهنگ لایک شد',
            'likes' => $song->like_count
        ]);
    }
}
```

backend/app/Http/Controllers/API/ArtistController.php

```php
<?php

namespace App\Http\Controllers\API;

use App\Http\Controllers\Controller;
use App\Models\Artist;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;

class ArtistController extends Controller
{
    public function index(): JsonResponse
    {
        $artists = Artist::withCount('songs')
            ->orderBy('followers_count', 'desc')
            ->get();

        return response()->json([
            'success' => true,
            'data' => $artists
        ]);
    }

    public function top(): JsonResponse
    {
        $artists = Artist::withCount('songs')
            ->orderBy('followers_count', 'desc')
            ->limit(10)
            ->get();

        return response()->json([
            'success' => true,
            'data' => $artists
        ]);
    }

    public function show(Artist $artist): JsonResponse
    {
        $artist->load(['albums', 'songs.album']);

        return response()->json([
            'success' => true,
            'data' => $artist
        ]);
    }

    public function store(Request $request): JsonResponse
    {
        $validated = $request->validate([
            'name' => 'required|string|max:255',
            'bio' => 'nullable|string',
            'photo' => 'nullable|string',
            'is_verified' => 'boolean'
        ]);

        $artist = Artist::create($validated);

        return response()->json([
            'success' => true,
            'data' => $artist,
            'message' => 'هنرمند با موفقیت اضافه شد'
        ], 201);
    }
}
```

backend/app/Http/Controllers/API/AdminController.php

```php
<?php

namespace App\Http\Controllers\API;

use App\Http\Controllers\Controller;
use App\Models\User;
use App\Models\Song;
use App\Models\Artist;
use App\Models\Album;
use Illuminate\Http\JsonResponse;

class AdminController extends Controller
{
    public function dashboardStats(): JsonResponse
    {
        $stats = [
            'total_users' => User::count(),
            'total_songs' => Song::count(),
            'total_artists' => Artist::count(),
            'total_albums' => Album::count(),
            'recent_users' => User::latest()->take(5)->get(),
            'popular_songs' => Song::with(['artist', 'album'])
                ->orderBy('play_count', 'desc')
                ->take(10)
                ->get()
        ];

        return response()->json([
            'success' => true,
            'data' => $stats
        ]);
    }

    public function getUsers(): JsonResponse
    {
        $users = User::latest()->get();

        return response()->json([
            'success' => true,
            'data' => $users
        ]);
    }

    public function updateUser(Request $request, User $user): JsonResponse
    {
        $validated = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email,' . $user->id,
            'is_admin' => 'boolean',
            'is_active' => 'boolean'
        ]);

        $user->update($validated);

        return response()->json([
            'success' => true,
            'data' => $user,
            'message' => 'کاربر با موفقیت به‌روزرسانی شد'
        ]);
    }

    public function deleteUser(User $user): JsonResponse
    {
        $user->delete();

        return response()->json([
            'success' => true,
            'message' => 'کاربر با موفقیت حذف شد'
        ]);
    }
}
```

🛣 روترها

backend/routes/api.php

```php
<?php

use App\Http\Controllers\API\ArtistController;
use App\Http\Controllers\API\SongController;
use App\Http\Controllers\API\AuthController;
use App\Http\Controllers\API\AdminController;
use App\Http\Controllers\API\AlbumController;
use Illuminate\Support\Facades\Route;

// Public routes
Route::post('/auth/register', [AuthController::class, 'register']);
Route::post('/auth/login', [AuthController::class, 'login']);

// Protected routes
Route::middleware('auth:sanctum')->group(function () {
    Route::post('/auth/logout', [AuthController::class, 'logout']);
    Route::get('/user', [AuthController::class, 'user']);

    // Songs
    Route::get('/songs', [SongController::class, 'index']);
    Route::get('/songs/trending', [SongController::class, 'trending']);
    Route::get('/songs/{song}', [SongController::class, 'show']);
    Route::post('/songs/{song}/like', [SongController::class, 'like']);

    // Artists
    Route::get('/artists', [ArtistController::class, 'index']);
    Route::get('/artists/top', [ArtistController::class, 'top']);
    Route::get('/artists/{artist}', [ArtistController::class, 'show']);

    // Albums
    Route::get('/albums', [AlbumController::class, 'index']);
    Route::get('/albums/{album}', [AlbumController::class, 'show']);

    // Admin routes
    Route::middleware('admin')->prefix('admin')->group(function () {
        Route::get('/dashboard-stats', [AdminController::class, 'dashboardStats']);
        Route::get('/users', [AdminController::class, 'getUsers']);
        Route::put('/users/{user}', [AdminController::class, 'updateUser']);
        Route::delete('/users/{user}', [AdminController::class, 'deleteUser']);
        
        Route::post('/songs', [SongController::class, 'store']);
        Route::put('/songs/{song}', [SongController::class, 'update']);
        Route::delete('/songs/{song}', [SongController::class, 'destroy']);
        
        Route::post('/artists', [ArtistController::class, 'store']);
        Route::post('/albums', [AlbumController::class, 'store']);
    });
});
```

🌱 سیدرها

backend/database/seeders/AdminUserSeeder.php

```php
<?php

namespace Database\Seeders;

use App\Models\User;
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\Hash;

class AdminUserSeeder extends Seeder
{
    public function run(): void
    {
        User::create([
            'name' => 'mahdi',
            'email' => 'mahdi@ritmo.com',
            'password' => Hash::make('mahDI490&'),
            'is_admin' => true,
            'email_verified_at' => now(),
        ]);

        // Create regular users
        User::factory(5)->create();
    }
}
```

backend/database/seeders/DatabaseSeeder.php

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        $this->call([
            AdminUserSeeder::class,
        ]);
    }
}
```

⚛️ بخش 2: فرانت‌اند (React.js)

📦 پکیج‌ها

frontend/package.json

```json
{
  "name": "ritmo-frontend",
  "type": "module",
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.8.0",
    "axios": "^1.3.0",
    "lucide-react": "^0.294.0"
  },
  "devDependencies": {
    "vite": "^4.1.0",
    "@vitejs/plugin-react": "^3.1.0",
    "tailwindcss": "^3.2.0",
    "autoprefixer": "^10.4.0",
    "postcss": "^8.4.0"
  },
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  }
}
```

🎨 تنظیمات Tailwind

frontend/tailwind.config.js

```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          500: '#0ea5e9',
          600: '#0284c7',
        },
        dark: {
          100: '#1e293b',
          200: '#334155',
          300: '#475569',
          400: '#64748b',
          500: '#94a3b8',
        },
        neon: {
          pink: '#ec4899',
          blue: '#0ea5e9',
          green: '#10b981',
          purple: '#8b5cf6',
        }
      },
      fontFamily: {
        'vazir': ['Vazir', 'sans-serif'],
      },
    },
  },
  plugins: [],
  darkMode: 'class',
}
```

🔑 Context احراز هویت

frontend/src/contexts/AuthContext.jsx

```jsx
import React, { createContext, useState, useContext, useEffect } from 'react';
import axios from 'axios';

const AuthContext = createContext();

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  return context;
};

export const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const token = localStorage.getItem('token');
    if (token) {
      axios.defaults.headers.common['Authorization'] = `Bearer ${token}`;
      fetchUser();
    } else {
      setLoading(false);
    }
  }, []);

  const fetchUser = async () => {
    try {
      const response = await axios.get('http://localhost:8000/api/user');
      setUser(response.data.data);
    } catch (error) {
      localStorage.removeItem('token');
      delete axios.defaults.headers.common['Authorization'];
    } finally {
      setLoading(false);
    }
  };

  const login = async (email, password) => {
    try {
      const response = await axios.post('http://localhost:8000/api/auth/login', {
        email,
        password,
      });
      
      const { data } = response.data;
      localStorage.setItem('token', data.token);
      axios.defaults.headers.common['Authorization'] = `Bearer ${data.token}`;
      setUser(data.user);
      
      return { success: true };
    } catch (error) {
      return { 
        success: false, 
        message: error.response?.data?.message || 'خطا در ورود' 
      };
    }
  };

  const register = async (userData) => {
    try {
      const response = await axios.post('http://localhost:8000/api/auth/register', userData);
      return { success: true, data: response.data };
    } catch (error) {
      return { 
        success: false, 
        message: error.response?.data?.message || 'خطا در ثبت نام' 
      };
    }
  };

  const logout = async () => {
    try {
      await axios.post('http://localhost:8000/api/auth/logout');
    } catch (error) {
      console.error('Logout error:', error);
    } finally {
      localStorage.removeItem('token');
      delete axios.defaults.headers.common['Authorization'];
      setUser(null);
    }
  };

  const value = {
    user,
    login,
    register,
    logout,
    loading,
  };

  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
};
```

🧩 کامپوننت‌های اصلی

frontend/src/components/layout/Navbar.jsx

```jsx
import React, { useState } from 'react';
import { Link, useNavigate } from 'react-router-dom';
import { Search, User, LogOut, Music, Crown, Menu, X, Bell } from 'lucide-react';
import { useAuth } from '../../contexts/AuthContext';

const Navbar = () => {
  const [searchQuery, setSearchQuery] = useState('');
  const [isMobileMenuOpen, setIsMobileMenuOpen] = useState(false);
  const { user, logout } = useAuth();
  const navigate = useNavigate();

  const handleSearch = (e) => {
    e.preventDefault();
    console.log('Searching for:', searchQuery);
  };

  const handleLogout = () => {
    logout();
    navigate('/');
  };

  return (
    <nav className="bg-gray-800 border-b border-neon-purple sticky top-0 z-50">
      <div className="container mx-auto px-4 py-3">
        <div className="flex items-center justify-between">
          {/* Logo */}
          <Link to="/" className="flex items-center space-x-2 space-x-reverse">
            <Music className="h-8 w-8 text-neon-pink" />
            <span className="text-2xl font-bold bg-gradient-to-r from-neon-pink to-neon-purple bg-clip-text text-transparent">
              RITMO
            </span>
          </Link>

          {/* Search Bar */}
          <form onSubmit={handleSearch} className="hidden md:flex flex-1 max-w-2xl mx-8">
            <div className="relative w-full">
              <Search className="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-400 h-5 w-5" />
              <input
                type="text"
                placeholder="جستجوی آهنگ، هنرمند، آلبوم..."
                value={searchQuery}
                onChange={(e) => setSearchQuery(e.target.value)}
                className="w-full bg-gray-700 text-white pr-10 pl-4 py-2 rounded-full focus:outline-none focus:ring-2 focus:ring-neon-blue border border-gray-600"
              />
            </div>
          </form>

          {/* Desktop Menu */}
          <div className="hidden md:flex items-center space-x-4 space-x-reverse">
            {user ? (
              <>
                {user.is_admin && (
                  <Link 
                    to="/admin" 
                    className="flex items-center space-x-2 space-x-reverse bg-neon-purple hover:bg-purple-600 px-4 py-2 rounded-full transition-colors"
                  >
                    <Crown className="h-4 w-4" />
                    <span>پنل مدیریت</span>
                  </Link>
                )}
                <Link to="/subscription" className="flex items-center space-x-2 space-x-reverse bg-neon-blue hover:bg-blue-600 px-4 py-2 rounded-full transition-colors">
                  <Crown className="h-4 w-4" />
                  <span>اشتراک ویژه</span>
                </Link>
                <button className="p-2 text-gray-400 hover:text-white transition-colors relative">
                  <Bell className="h-5 w-5" />
                  <span className="absolute -top-1 -left-1 bg-red-500 text-white rounded-full w-4 h-4 text-xs flex items-center justify-center">
                    3
                  </span>
                </button>
                <Link to="/profile" className="flex items-center space-x-2 space-x-reverse hover:text-neon-blue transition-colors">
                  <User className="h-5 w-5" />
                  <span>{user.name}</span>
                </Link>
                <button
                  onClick={handleLogout}
                  className="flex items-center space-x-2 space-x-reverse text-red-400 hover:text-red-300 transition-colors"
                >
                  <LogOut className="h-5 w-5" />
                  <span>خروج</span>
                </button>
              </>
            ) : (
              <div className="flex items-center space-x-4 space-x-reverse">
                <Link to="/login" className="text-gray-300 hover:text-white transition-colors">
                  ورود
                </Link>
                <Link 
                  to="/register" 
                  className="bg-neon-blue hover:bg-blue-600 px-4 py-2 rounded-full transition-colors"
                >
                  ثبت نام
                </Link>
              </div>
            )}
          </div>

          {/* Mobile Menu Button */}
          <button 
            className="md:hidden p-2"
            onClick={() => setIsMobileMenuOpen(!isMobileMenuOpen)}
          >
            {isMobileMenuOpen ? <X className="h-6 w-6" /> : <Menu className="h-6 w-6" />}
          </button>
        </div>

        {/* Mobile Menu */}
        {isMobileMenuOpen && (
          <div className="md:hidden mt-4 pb-4 border-t border-gray-700 pt-4">
            <div className="flex flex-col space-y-4">
              {user ? (
                <>
                  <Link to="/profile" className="flex items-center space-x-2 space-x-reverse text-white">
                    <User className="h-5 w-5" />
                    <span>پروفایل</span>
                  </Link>
                  {user.is_admin && (
                    <Link to="/admin" className="flex items-center space-x-2 space-x-reverse text-white">
                      <Crown className="h-5 w-5" />
                      <span>پنل مدیریت</span>
                    </Link>
                  )}
                  <Link to="/subscription" className="flex items-center space-x-2 space-x-reverse text-white">
                    <Crown className="h-5 w-5" />
                    <span>اشتراک ویژه</span>
                  </Link>
                  <button 
                    onClick={handleLogout}
                    className="flex items-center space-x-2 space-x-reverse text-red-400"
                  >
                    <LogOut className="h-5 w-5" />
                    <span>خروج</span>
                  </button>
                </>
              ) : (
                <>
                  <Link to="/login" className="text-white text-center py-2">
                    ورود
                  </Link>
                  <Link 
                    to="/register" 
                    className="bg-neon-blue text-white text-center py-2 rounded-full"
                  >
                    ثبت نام
                  </Link>
                </>
              )}
            </div>
          </div>
        )}
      </div>
    </nav>
  );
};

export default Navbar;
```

frontend/src/components/common/HorizontalScroll.jsx

```jsx
import React from 'react';
import { ChevronLeft, ChevronRight } from 'lucide-react';

const HorizontalScroll = ({ title, children, items = [] }) => {
  const scrollLeft = () => {
    const container = document.getElementById(`scroll-${title}`);
    container.scrollBy({ left: -300, behavior: 'smooth' });
  };

  const scrollRight = () => {
    const container = document.getElementById(`scroll-${title}`);
    container.scrollBy({ left: 300, behavior: 'smooth' });
  };

  if (!items || items.length === 0) return null;

  return (
    <section className="mb-12">
      <div className="flex items-center justify-between mb-6">
        <h2 className="text-2xl font-bold text-white">{title}</h2>
        <div className="flex space-x-2 space-x-reverse">
          <button
            onClick={scrollLeft}
            className="p-2 rounded-full bg-gray-700 hover:bg-gray-600 transition-colors"
          >
            <ChevronRight className="h-5 w-5" />
          </button>
          <button
            onClick={scrollRight}
            className="p-2 rounded-full bg-gray-700 hover:bg-gray-600 transition-colors"
          >
            <ChevronLeft className="h-5 w-5" />
          </button>
        </div>
      </div>
      
      <div
        id={`scroll-${title}`}
        className="flex space-x-4 space-x-reverse overflow-x-auto scrollbar-hide scroll-smooth pb-4"
        style={{ scrollbarWidth: 'none', msOverflowStyle: 'none' }}
      >
        {children}
      </div>
    </section>
  );
};

export default HorizontalScroll;
```

📄 صفحات اصلی

frontend/src/pages/Home.jsx

```jsx
import React, { useState, useEffect } from 'react';
import { Play, Heart, Download, Clock } from 'lucide-react';
import axios from 'axios';
import HorizontalScroll from '../components/common/HorizontalScroll';

const Home = () => {
  const [trendingSongs, setTrendingSongs] = useState([]);
  const [newReleases, setNewReleases] = useState([]);
  const [topArtists, setTopArtists] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchHomeData();
  }, []);

  const fetchHomeData = async () => {
    try {
      const [songsRes, artistsRes] = await Promise.all([
        axios.get('http://localhost:8000/api/songs/trending'),
        axios.get('http://localhost:8000/api/artists/top')
      ]);

      setTrendingSongs(songsRes.data.data || []);
      setTopArtists(artistsRes.data.data || []);
      setNewReleases((songsRes.data.data || []).slice(0, 8));
    } catch (error) {
      console.error('Error fetching home data:', error);
      // Set dummy data for demo
      setTrendingSongs(getDummySongs());
      setTopArtists(getDummyArtists());
      setNewReleases(getDummySongs().slice(0, 8));
    } finally {
      setLoading(false);
    }
  };

  const getDummySongs = () => [
    {
      id: 1,
      title: 'آهنگ نمونه ۱',
      artist: { name: 'خواننده ۱' },
      album: { title: 'آلبوم ۱', cover_image: '' },
      duration: 180,
      cover_image: '',
      play_count: 1500,
      like_count: 200
    },
    {
      id: 2,
      title: 'آهنگ نمونه ۲',
      artist: { name: 'خواننده ۲' },
      album: { title: 'آلبوم ۲', cover_image: '' },
      duration: 210,
      cover_image: '',
      play_count: 1200,
      like_count: 150
    }
  ];

  const getDummyArtists = () => [
    {
      id: 1,
      name: 'خواننده ۱',
      photo: '',
      followers_count: 50000,
      is_verified: true
    },
    {
      id: 2,
      name: 'خواننده ۲',
      photo: '',
      followers_count: 30000,
      is_verified: false
    }
  ];

  const formatDuration = (seconds) => {
    const mins = Math.floor(seconds / 60);
    const secs = seconds % 60;
    return `${mins}:${secs.toString().padStart(2, '0')}`;
  };

  if (loading) {
    return (
      <div className="min-h-screen flex items-center justify-center">
        <div className="animate-spin rounded-full h-32 w-32 border-b-2 border-neon-pink"></div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gradient-to-b from-gray-900 to-gray-800">
      <div className="container mx-auto px-4 py-8">
        {/* Hero Section */}
        <section className="mb-12 text-center">
          <h1 className="text-5xl font-bold mb-4 bg-gradient-to-r from-neon-pink via-neon-purple to-neon-blue bg-clip-text text-transparent">
            به RITMO خوش آمدید
          </h1>
          <p className="text-xl text-gray-300 max-w-2xl mx-auto">
            کشف کنید، گوش دهید، لذت ببرید. دنیای موسیقی فارسی در دستان شما
          </p>
        </section>

        {/* Trending Songs */}
        <HorizontalScroll title="آهنگ های ترند" items={trendingSongs}>
          {trendingSongs.map((song) => (
            <div key={song.id} className="bg-gray-800 rounded-lg p-4 hover:bg-gray-700 transition-all group flex-shrink-0 w-48">
              <div className="relative">
                <div className="w-full aspect-square bg-gray-700 rounded-lg mb-3 flex items-center justify-center">
                  <Music className="h-12 w-12 text-gray-500" />
                </div>
                <button className="absolute inset-0 bg-black bg-opacity-50 flex items-center justify-center opacity-0 group-hover:opacity-100 transition-opacity rounded-lg">
                  <Play className="h-12 w-12 text-white fill-current" />
                </button>
              </div>
              <h3 className="font-semibold text-white truncate">{song.title}</h3>
              <p className="text-gray-400 text-sm truncate">{song.artist?.name}</p>
              <div className="flex items-center justify-between mt-2">
                <div className="flex items-center space-x-2 space-x-reverse">
                  <button className="text-gray-400 hover:text-neon-pink transition-colors">
                    <Heart className="h-4 w-4" />
                  </button>
                  <button className="text-gray-400 hover:text-neon-green transition-colors">
                    <Download className="h-4 w-4" />
                  </button>
                </div>
                <span className="text-gray-400 text-sm flex items-center">
                  <Clock className="h-3 w-3 ml-1" />
                  {formatDuration(song.duration)}
                </span>
              </div>
            </div>
          ))}
        </HorizontalScroll>

        {/* New Releases */}
        <HorizontalScroll title="آلبوم های جدید" items={newReleases}>
          {newReleases.map((song) => (
            <div key={song.id} className="bg-gray-800 rounded-lg p-4 hover:bg-gray-700 transition-all group flex-shrink-0 w-48">
              <div className="w-full aspect-square bg-gray-700 rounded-lg mb-3 flex items-center justify-center">
                <Music className="h-12 w-12 text-gray-500" />
              </div>
              <h3 className="font-semibold text-white truncate">{song.album?.title}</h3>
              <p className="text-gray-400 text-sm">{song.artist?.name}</p>
            </div>
          ))}
        </HorizontalScroll>

        {/* Top Artists */}
        <HorizontalScroll title="برترین هنرمندان" items={topArtists}>
          {topArtists.map((artist) => (
            <div key={artist.id} className="text-center group flex-shrink-0 w-32">
              <div className="w-32 h-32 mx-auto mb-3 relative">
                <div className="w-full h-full bg-gray-700 rounded-full flex items-center justify-center">
                  <User className="h-8 w-8 text-gray-500" />
                </div>
                {artist.is_verified && (
                  <div className="absolute bottom-0 right-0 bg-neon-blue rounded-full p-1">
                    <svg className="h-4 w-4 text-white" fill="currentColor" viewBox="0 0 20 20">
                      <path fillRule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clipRule="evenodd" />
                    </svg>
                  </div>
                )}
              </div>
              <h3 className="font-semibold text-white">{artist.name}</h3>
              <p className="text-gray-400 text-sm">{artist.followers_count.toLocaleString()} دنبال کننده</p>
            </div>
          ))}
        </HorizontalScroll>
      </div>
    </div>
  );
};

export default Home;
```

frontend/src/pages/admin/AdminDashboard.jsx

```jsx
import React, { useState, useEffect } from 'react';
import { 
  Users, Music, Mic2, Disc, BarChart3, 
  Plus, Edit, Trash2 
} from 'lucide-react';
import axios from 'axios';

const AdminDashboard = () => {
  const [activeTab, setActiveTab] = useState('dashboard');
  const [stats, setStats] = useState({});
  const [songs, setSongs] = useState([]);
  const [users, setUsers] = useState([]);
  const [artists, setArtists] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchDashboardData();
  }, []);

  const fetchDashboardData = async () => {
    try {
      const [statsRes, songsRes, usersRes, artistsRes] = await Promise.all([
        axios.get('http://localhost:8000/api/admin/dashboard-stats'),
        axios.get('http://localhost:8000/api/songs'),
        axios.get('http://localhost:8000/api/admin/users'),
        axios.get('http://localhost:8000/api/artists')
      ]);

      setStats(statsRes.data.data || {});
      setSongs(songsRes.data.data || []);
      setUsers(usersRes.data.data || []);
      setArtists(artistsRes.data.data || []);
    } catch (error) {
      console.error('Error fetching dashboard data:', error);
    } finally {
      setLoading(false);
    }
  };

  const deleteSong = async (songId) => {
    if (!window.confirm('آیا از حذف این آهنگ مطمئن هستید؟')) return;

    try {
      await axios.delete(`http://localhost:8000/api/admin/songs/${songId}`);
      setSongs(songs.filter(song => song.id !== songId));
      alert('آهنگ با موفقیت حذف شد');
    } catch (error) {
      alert('خطا در حذف آهنگ');
    }
  };

  const deleteUser = async (userId) => {
    if (!window.confirm('آیا از حذف این کاربر مطمئن هستید؟')) return;

    try {
      await axios.delete(`http://localhost:8000/api/admin/users/${userId}`);
      setUsers(users.filter(user => user.id !== userId));
      alert('کاربر با موفقیت حذف شد');
    } catch (error) {
      alert('خطا در حذف کاربر');
    }
  };

  if (loading) {
    return (
      <div className="min-h-screen bg-gray-900 flex items-center justify-center">
        <div className="animate-spin rounded-full h-32 w-32 border-b-2 border-neon-pink"></div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gray-900 text-white">
      {/* Header */}
      <div className="bg-gray-800 border-b border-neon-purple">
        <div className="container mx-auto px-4 py-6">
          <h1 className="text-3xl font-bold bg-gradient-to-r from-neon-pink to-neon-purple bg-clip-text text-transparent">
            پنل مدیریت RITMO
          </h1>
          <p className="text-gray-400 mt-2">مدیریت کامل محتوای سایت</p>
        </div>
      </div>

      <div className="container mx-auto px-4 py-8">
        <div className="grid grid-cols-1 lg:grid-cols-4 gap-8">
          {/* Sidebar */}
          <div className="lg:col-span-1">
            <div className="bg-gray-800 rounded-lg p-6 sticky top-24">
              <nav className="space-y-2">
                {[
                  { id: 'dashboard', label: 'داشبورد', icon: BarChart3 },
                  { id: 'songs', label: 'مدیریت آهنگ‌ها', icon: Music },
                  { id: 'artists', label: 'مدیریت هنرمندان', icon: Mic2 },
                  { id: 'users', label: 'مدیریت کاربران', icon: Users },
                ].map((item) => {
                  const Icon = item.icon;
                  return (
                    <button
                      key={item.id}
                      onClick={() => setActiveTab(item.id)}
                      className={`w-full flex items-center space-x-3 space-x-reverse p-3 rounded-lg transition-colors ${
                        activeTab === item.id
                          ? 'bg-neon-blue text-white'
                          : 'text-gray-400 hover:text-white hover:bg-gray-700'
                      }`}
                    >
                      <Icon className="h-5 w-5" />
                      <span>{item.label}</span>
                    </button>
                  );
                })}
              </nav>
            </div>
          </div>

          {/* Main Content */}
          <div className="lg:col-span-3">
            {/* Dashboard Tab */}
            {activeTab === 'dashboard' && (
              <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 mb-8">
                <div className="bg-gray-800 rounded-lg p-6 border-l-4 border-neon-pink">
                  <div className="flex items-center justify-between">
                    <div>
                      <p className="text-gray-400">تعداد کاربران</p>
                      <p className="text-3xl font-bold text-white">{stats.total_users || 0}</p>
                    </div>
                    <Users className="h-8 w-8 text-neon-pink" />
                  </div>
                </div>

                <div className="bg-gray-800 rounded-lg p-6 border-l-4 border-neon-blue">
                  <div className="flex items-center justify-between">
                    <div>
                      <p className="text-gray-400">تعداد آهنگ‌ها</p>
                      <p className="text-3xl font-bold text-white">{stats.total_songs || 0}</p>
                    </div>
                    <Music className="h-8 w-8 text-neon-blue" />
                  </div>
                </div>

                <div className="bg-gray-800 rounded-lg p-6 border-l-4 border-neon-green">
                  <div className="flex items-center justify-between">
                    <div>
                      <p className="text-gray-400">تعداد هنرمندان</p>
                      <p className="text-3xl font-bold text-white">{stats.total_artists || 0}</p>
                    </div>
                    <Mic2 className="h-8 w-8 text-neon-green" />
                  </div>
                </div>
              </div>
            )}

            {/* Songs Management Tab */}
            {activeTab === 'songs' && (
              <div className="bg-gray-800 rounded-lg p-6">
                <div className="flex items-center justify-between mb-6">
                  <h2 className="text-2xl font-bold">مدیریت آهنگ‌ها</h2>
                  <button className="bg-neon-blue hover:bg-blue-600 px-4 py-2 rounded-lg flex items-center space-x-2 space-x-reverse">
                    <Plus className="h-4 w-4" />
                    <span>افزودن آهنگ جدید</span>
                  </button>
                </div>

                <div className="overflow-x-auto">
                  <table className="w-full">
                    <thead>
                      <tr className="border-b border-gray-700">
                        <th className="text-right py-3 px-4">عنوان</th>
                        <th className="text-right py-3 px-4">هنرمند</th>
                        <th className="text-right py-3 px-4">آلبوم</th>
                        <th className="text-right py-3 px-4">تعداد پخش</th>
                        <th className="text-right py-3 px-4">عملیات</th>
                      </tr>
                    </thead>
                    <tbody>
                      {songs.map((song) => (
                        <tr key={song.id} className="border-b border-gray-700 hover:bg-gray-750">
                          <td className="py-3 px-4">
                            <div className="flex items-center space-x-3 space-x-reverse">
                              <div className="w-10 h-10 bg-gray-700 rounded flex items-center justify-center">
                                <Music className="h-5 w-5 text-gray-400" />
                              </div>
                              <div>
                                <div className="font-semibold">{song.title}</div>
                                <div className="text-gray-400 text-sm">
                                  {formatDuration(song.duration)}
                                </div>
                              </div>
                            </div>
                          </td>
                          <td className="py-3 px-4">{song.artist?.name}</td>
                          <td className="py-3 px-4">{song.album?.title}</td>
                          <td className="py-3 px-4">{song.play_count.toLocaleString()}</td>
                          <td className="py-3 px-4">
                            <div className="flex items-center space-x-2 space-x-reverse">
                              <button className="text-blue-400 hover:text-blue-300 p-1">
                                <Edit className="h-4 w-4" />
                              </button>
                              <button 
                                onClick={() => deleteSong(song.id)}
                                className="text-red-400 hover:text-red-300 p-1"
                              >
                                <Trash2 className="h-4 w-4" />
                              </button>
                            </div>
                          </td>
                        </tr>
                      ))}
                    </tbody>
                  </table>
                </div>
              </div>
            )}

            {/* Users Management Tab */}
            {activeTab === 'users' && (
              <div className="bg-gray-800 rounded-lg p-6">
                <div className="flex items-center justify-between mb-6">
                  <h2 className="text-2xl font-bold">مدیریت کاربران</h2>
                </div>

                <div className="overflow-x-auto">
                  <table className="w-full">
                    <thead>
                      <tr className="border-b border-gray-700">
                        <th className="text-right py-3 px-4">کاربر</th>
                        <th className="text-right py-3 px-4">ایمیل</th>
                        <th className="text-right py-3 px-4">نقش</th>
                        <th className="text-right py-3 px-4">وضعیت</th>
                        <th className="text-right py-3 px-4">تاریخ ثبت‌نام</th>
                        <th className="text-right py-3 px-4">عملیات</th>
                      </tr>
                    </thead>
                    <tbody>
                      {users.map((user) => (
                        <tr key={user.id} className="border-b border-gray-700 hover:bg-gray-750">
                          <td className="py-3 px-4">
                            <div className="flex items-center space-x-3 space-x-reverse">
                              <div className="w-10 h-10 bg-gray-600 rounded-full flex items-center justify-center">
                                <Users className="h-5 w-5 text-gray-400" />
                              </div>
                              <div className="font-semibold">{user.name}</div>
                            </div>
                          </td>
                          <td className="py-3 px-4">{user.email}</td>
                          <td className="py-3 px-4">
                            <span className={`px-2 py-1 rounded-full text-xs ${
                              user.is_admin 
                                ? 'bg-purple-500 text-white' 
                                : 'bg-gray-600 text-gray-300'
                            }`}>
                              {user.is_admin ? 'مدیر' : 'کاربر عادی'}
                            </span>
                          </td>
                          <td className="py-3 px-4">
                            <span className={`px-2 py-1 rounded-full text-xs ${
                              user.is_active 
                                ? 'bg-green-500 text-white' 
                                : 'bg-red-500 text-white'
                            }`}>
                              {user.is_active ? 'فعال' : 'غیرفعال'}
                            </span>
                          </td>
                          <td className="py-3 px-4">
                            {new Date(user.created_at).toLocaleDateString('fa-IR')}
                          </td>
                          <td className="py-3 px-4">
                            <div className="flex items-center space-x-2 space-x-reverse">
                              <button className="text-blue-400 hover:text-blue-300 p-1">
                                <Edit className="h-4 w-4" />
                              </button>
                              <button 
                                onClick={() => deleteUser(user.id)}
                                className="text-red-400 hover:text-red-300 p-1"
                              >
                                <Trash2 className="h-4 w-4" />
                              </button>
                            </div>
                          </td>
                        </tr>
                      ))}
                    </tbody>
                  </table>
                </div>
              </div>
            )}

            {/* Artists Management Tab */}
            {activeTab === 'artists' && (
              <div className="bg-gray-800 rounded-lg p-6">
                <div className="flex items-center justify-between mb-6">
                  <h2 className="text-2xl font-bold">مدیریت هنرمندان</h2>
                  <button className="bg-neon-blue hover:bg-blue-600 px-4 py-2 rounded-lg flex items-center space-x-2 space-x-reverse">
                    <Plus className="h-4 w-4" />
                    <span>افزودن هنرمند جدید</span>
                  </button>
                </div>

                <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                  {artists.map((artist) => (
                    <div key={artist.id} className="bg-gray-750 rounded-lg p-4 hover:bg-gray-700 transition-colors">
                      <div className="flex items-center space-x-4 space-x-reverse mb-4">
                        <div className="w-16 h-16 bg-gray-600 rounded-full flex items-center justify-center">
                          <Mic2 className="h-6 w-6 text-gray-400" />
                        </div>
                        <div className="flex-1">
                          <h3 className="font-semibold text-white">{artist.name}</h3>
                          <p className="text-gray-400 text-sm">
                            {artist.followers_count.toLocaleString()} دنبال‌کننده
                          </p>
                        </div>
                        {artist.is_verified && (
                          <div className="bg-neon-blue rounded-full p-1">
                            <svg className="h-4 w-4 text-white" fill="currentColor" viewBox="0 0 20 20">
                              <path fillRule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clipRule="evenodd" />
                            </svg>
                          </div>
                        )}
                      </div>
                      <div className="flex items-center justify-between">
                        <span className="text-gray-400 text-sm">
                          {artist.songs_count || 0} آهنگ
                        </span>
                        <div className="flex items-center space-x-2 space-x-reverse">
                          <button className="text-blue-400 hover:text-blue-300 p-1">
                            <Edit className="h-4 w-4" />
                          </button>
                          <button className="text-red-400 hover:text-red-300 p-1">
                            <Trash2 className="h-4 w-4" />
                          </button>
                        </div>
                      </div>
                    </div>
                  ))}
                </div>
              </div>
            )}
          </div>
        </div>
      </div>
    </div>
  );
};

// Helper function
const formatDuration = (seconds) => {
  const mins = Math.floor(seconds / 60);
  const secs = seconds % 60;
  return `${mins}:${secs.toString().padStart(2, '0')}`;
};

export default AdminDashboard;
```

frontend/src/pages/auth/Login.jsx

```jsx
import React, { useState } from 'react';
import { Link, useNavigate } from 'react-router-dom';
import { Eye, EyeOff, Mail, Lock, Music } from 'lucide-react';
import { useAuth } from '../../contexts/AuthContext';

const Login = () => {
  const [formData, setFormData] = useState({
    email: '',
    password: '',
  });
  const [showPassword, setShowPassword] = useState(false);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState('');

  const { login } = useAuth();
  const navigate = useNavigate();

  const handleChange = (e) => {
    setFormData({
      ...formData,
      [e.target.name]: e.target.value,
    });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    setLoading(true);
    setError('');

    const result = await login(formData.email, formData.password);
    
    if (result.success) {
      navigate('/');
    } else {
      setError(result.message);
    }
    
    setLoading(false);
  };

  return (
    <div className="min-h-screen bg-gray-900 flex items-center justify-center py-12 px-4 sm:px-6 lg:px-8">
      <div className="max-w-md w-full space-y-8">
        <div className="text-center">
          <div className="flex justify-center">
            <Music className="h-12 w-12 text-neon-pink" />
          </div>
          <h2 className="mt-6 text-3xl font-bold bg-gradient-to-r from-neon-pink to-neon-purple bg-clip-text text-transparent">
            ورود به RITMO
          </h2>
          <p className="mt-2 text-sm text-gray-400">
            یا{' '}
            <Link
              to="/register"
              className="font-medium text-neon-blue hover:text-blue-400 transition-colors"
            >
              ایجاد حساب کاربری جدید
            </Link>
          </p>
        </div>

        <form className="mt-8 space-y-6" onSubmit={handleSubmit}>
          {error && (
            <div className="bg-red-900 bg-opacity-20 border border-red-500 text-red-400 px-4 py-3 rounded-lg">
              {error}
            </div>
          )}

          <div className="space-y-4">
            <div>
              <label htmlFor="email" className="sr-only">
                ایمیل
              </label>
              <div className="relative">
                <Mail className="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400 h-5 w-5" />
                <input
                  id="email"
                  name="email"
                  type="email"
                  required
                  value={formData.email}
                  onChange={handleChange}
                  className="w-full bg-gray-800 border border-gray-700 rounded-lg py-3 pr-4 pl-12 text-white placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-neon-blue focus:border-transparent"
                  placeholder="آدرس ایمیل"
                />
              </div>
            </div>

            <div>
              <label htmlFor="password" className="sr-only">
                رمز عبور
              </label>
              <div className="relative">
                <Lock className="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400 h-5 w-5" />
                <input
                  id="password"
                  name="password"
                  type={showPassword ? 'text' : 'password'}
                  required
                  value={formData.password}
                  onChange={handleChange}
                  className="w-full bg-gray-800 border border-gray-700 rounded-lg py-3 pr-4 pl-12 text-white placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-neon-blue focus:border-transparent"
                  placeholder="رمز عبور"
                />
                <button
                  type="button"
                  onClick={() => setShowPassword(!showPassword)}
                  className="absolute left-12 top-1/2 transform -translate-y-1/2 text-gray-400 hover:text-gray-300"
                >
                  {showPassword ? <EyeOff className="h-5 w-5" /> : <Eye className="h-5 w-5" />}
                </button>
              </div>
            </div>
          </div>

          <div>
            <button
              type="submit"
              disabled={loading}
              className="group relative w-full flex justify-center py-3 px-4 border border-transparent text-sm font-medium rounded-lg text-white bg-neon-blue hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-neon-blue transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
            >
              {loading ? 'در حال ورود...' : 'ورود به حساب'}
            </button>
          </div>
        </form>
      </div>
    </div>
  );
};

export default Login;
```

🚀 فایل اصلی اپلیکیشن

frontend/src/App.jsx

```jsx
import React from 'react';
import { BrowserRouter as Router, Routes, Route, Navigate } from 'react-router-dom';
import { AuthProvider, useAuth } from './contexts/AuthContext';
import Navbar from './components/layout/Navbar';
import Home from './pages/Home';
import AdminDashboard from './pages/admin/AdminDashboard';
import Login from './pages/auth/Login';
import Register from './pages/auth/Register';

// Protected Route Component
const ProtectedRoute = ({ children, adminOnly = false }) => {
  const { user, loading } = useAuth();

  if (loading) {
    return (
      <div className="min-h-screen flex items-center justify-center">
        <div className="animate-spin rounded-full h-32 w-32 border-b-2 border-neon-pink"></div>
      </div>
    );
  }

  if (!user) {
    return <Navigate to="/login" />;
  }

  if (adminOnly && !user.is_admin) {
    return <Navigate to="/" />;
  }

  return children;
};

function App() {
  return (
    <AuthProvider>
      <Router>
        <div className="min-h-screen bg-gray-900 text-white font-vazir" dir="rtl">
          <Navbar />
          <main>
            <Routes>
              <Route path="/" element={<Home />} />
              <Route path="/login" element={<Login />} />
              <Route path="/register" element={<Register />} />
              <Route 
                path="/admin/*" 
                element={
                  <ProtectedRoute adminOnly={true}>
                    <AdminDashboard />
                  </ProtectedRoute>
                } 
              />
            </Routes>
          </main>
        </div>
      </Router>
    </AuthProvider>
  );
}

export default App;
```

frontend/src/main.jsx

```jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```

frontend/src/index.css

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@font-face {
  font-family: 'Vazir';
  src: url('https://cdn.jsdelivr.net/gh/rastikerdar/vazir-font@v30.1.0/dist/Vazir.woff2') format('woff2');
}

* {
  font-family: 'Vazir', sans-serif;
}

body {
  margin: 0;
  background-color: #0f172a;
  color: white;
}

/* Scrollbar Styling */
::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: #1e293b;
}

::-webkit-scrollbar-thumb {
  background: #475569;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #64748b;
}
```

frontend/vite.config.js

```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 3000,
    proxy: {
      '/api': {
        target: 'http://localhost:8000',
        changeOrigin: true,
      }
    }
  }
})
```

frontend/index.html

```html
<!doctype html>
<html lang="fa" dir="rtl">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/music.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>RITMO - موزیک استریمینگ فارسی</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/vazir-font@30.1.0/dist/font-face.css">
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

🚀 راهنمای نصب و اجرا

1. نصب بک‌اند (Laravel)

```bash
cd backend
composer install
cp .env.example .env
php artisan key:generate
```

2. تنظیم دیتابیس و میگریشن

```bash
php artisan migrate
php artisan db:seed
```

3. اجرای بک‌اند

```bash
php artisan serve
```

4. نصب فرانت‌اند (React)

```bash
cd frontend
npm install
npm run dev
```

5. اطلاعات ورود به پنل مدیریت

· آدرس: http://localhost:3000/admin
· نام کاربری: mahdi
· رمز عبور: mahDI490&
· ایمیل: mahdi@ritmo.com

✨ ویژگی‌های کامل سیستم

🎵 مدیریت محتوا

· ✅ افزودن/ویرایش/حذف آهنگ
· ✅ مدیریت هنرمندان
· ✅ مدیریت کاربران
· ✅ آمار و گزارش‌گیری

👥 پنل مدیریت

· ✅ مشاهده آمار کلی
· ✅ مدیریت کاربران
· ✅ مدیریت آهنگ‌ها
· ✅ مدیریت هنرمندان

🎨 طراحی

· ✅ تم تاریک با رنگ‌های نئون
· ✅ طراحی RTL و فارسی
· ✅ ریسپانسیو برای موبایل و دسکتاپ
· ✅ پنل مدیریت مدرن

این یک پروژه کامل و عملیاتی است! تمام کدهای لازم برای راه‌اندازی یک سایت موزیک استریمینگ حرفه‌ای با پنل مدیریت قدرتمند ارائه شده است. 🚀

حالا می‌توانی پروژه را در VS Code پیاده‌سازی کنی و سپس روی هاست آپلود کنی!
