# **Tutorial Pembuatan Game Dino**

## **Tugas Pemrograman Gim A 2024**

Kelompok 8 \- Mewing Studio

**Anggota Kelompok**:

- Faiqurrijal Tamhidy 225150200111041  
- A  
- Jeyhan Farrel Alfarisqi 225150207111082  
- a

[Link Github](https://github.com/qurreee/PemrogramanGame.DinoAyam)

(nulis kode kayaknya nanti gampang langsung di github, kalian kasi catatan mana kode yang harus dimasukin nanti ku masukin… kasih \`\`code\`\` biar keliatan )

## **Langkah pembuatan**

1. ### Persiapan File

2. ### Pembuatan GUI Main menu

3. ### Kelas Cactus.cpp

4. ### Inisialisasi Ingame DinoDino.cpp

5. ### Update Ingame 

   Di dalam File DinoDino.cpp yang merupakan Screen Ingame utama kita, akan ada fungsi Update seperti kelas-kelas yang ada sebelumnya. Di dalam fungsi ini, kita akan mengupdate semua objek yang dibutuhkan ketika game dimulai, seperti Sprite Pemain utama, hingga cactus yang muncul. Tidak lupa untuk melakukan Collision Detection antara sprite pemain dan cactus yang lewat, dan melakukan aksi yang diperlukan ketika kondisi Game Over terpenuhi.  
   

```cpp
  sprite->Update(game->GetGameTime());
  
  for (auto& cactus : cacti) {
  	cactus->Update(game->GetGameTime());
    //cek kolisi yang terjadi
  	if (sprite->GetBoundingBox()->CollideWith(cactus->GetBoundingBox())) {
  		ScreenManager::GetInstance(game)->SetCurrentScreen("mainmenu");
  		scoreTime = 0;
  		score = 0;
  		text->SetText("score " + std::to_string(score));
  		std::random_device rd;
  		std::mt19937 gen(rd());
  		std::uniform_int_distribution<> distrib(0, 3200);
  		int randomOffset = distrib(gen);
  		for (auto& cactus : cacti) {
  			cactus->SetPosition(1600 + randomOffset, 0);
  		}	
  	}
  }
```

Setelah itu, di dalam fungsi Update, kita juga dapat menentukan algoritma yang terjadi ketika kita menekan salah satu tombol input. Kita akan melakukan aksi yang terjadi ke input ***Jump*** 

```cpp
  vec2 oldPos = sprite->GetPosition();
  float x = oldPos.x, y = oldPos.y;
  sprite->SetPosition(x, y);
  //jump
  if (game->GetInputManager()->IsKeyPressed("jump") && !jump)
  {
  	float ratio = (game->GetGameTime() / 16.7f);
  	gravity = 0.14f * ratio;
  	yVelocity = 2.3f;
  	jump = true;
  	sprite->PlayAnim("jump");
  	/*sound->Play(false);*/
  }
  
  if (y > 0) {
  	yVelocity -= gravity;
  }
  else if (y < 0) {
  	jump = false;
  	yVelocity = 0;
  	y = 0;
  	sprite->PlayAnim("walk");
  }
  
  y += yVelocity * game->GetGameTime();
  sprite->SetPosition(x, y);
```

Kode diatas memastikan kita tidak dapat melakukan double jump, dan mengimplementasikan gravitasi yang perlahan menarik Sprite Character turun.  
Kita Juga akan melakukan perhitungan skor di dalam fungsi Update, yang akan dihitung setelah *ingametime* berjalan sebanyak nilai tertentu. Disini kita menyediakan Objek \`scoretime\` yang melakukan perhitungan tersebut, dan apabila

```cpp
  scoreTime += game->GetGameTime();
  if (scoreTime >= 1000.0f)
  {
  	score += 5; // Tambahkan skor berdasarkan waktu yang terakumulasi
  	scoreTime = 0.0f; // Reset scoreTime
  	text->SetText("score " + std::to_string(score));
  }
```
 
Maka score akan bertambah dan scoretime akan direset

6. ### Penambahan  Parallax

   Untuk Mempercantik Game yang dibuat, kita dapat mengimplementasikan Parallax Background yaitu background bergerak yang terus menerus terhubung sehingga ilusi character sedang berjalan dapat tergambarkan
	  
	Kode init Parallax  
```cpp
  //parallax
  for (int i = 0; i <= 4; i++) {
  	AddToLayer(backgrounds, "bg0" + to_string(i) + ".png");
  }
  for (int i = 5; i <= 8; i++) {
  	AddToLayer(middlegrounds, "bg0" + to_string(i) + ".png");
  }
  for (int i = 9; i <= 9; i++) {
  	AddToLayer(foregrounds, "bg0" + to_string(i) + ".png");
  }
  
  offset = 2;
```
  Fungsi utama Parallax  
```cpp
  void Engine::DinoDino::MoveLayer(vector<Sprite*>& bg, float speed)
  {
  	for (Sprite* s : bg) {
  		if (s->GetPosition().x < - game->GetSettings()->screenWidth + offset) {
  			s->SetPosition(game->GetSettings()->screenWidth + offset - 1, 0);
  		}
  		s->SetPosition(s->GetPosition().x - speed * game->GetGameTime(), s->GetPosition().y);
  		s->Update(game->GetGameTime());
  	}
  }
  
  void Engine::DinoDino::DrawLayer(vector<Sprite*>& bg)
  {
  	for (Sprite* s : bg) {
  		s->Draw();
  	}
  }
  
  void Engine::DinoDino::AddToLayer(vector<Sprite*>& bg, string name)
  {
  	Texture* texture1 = new Texture(name);
  
  	Sprite* s = new Sprite(texture1, game->GetDefaultSpriteShader(), game->GetDefaultQuad());
  	s->SetSize(game->GetSettings()->screenWidth + offset, game->GetSettings()->screenHeight);
  	bg.push_back(s);
  
  	Sprite* s2 = new Sprite(texture1, game->GetDefaultSpriteShader(), game->GetDefaultQuad());
  	s2->SetSize(game->GetSettings()->screenWidth + offset, game->GetSettings()->screenHeight)->SetPosition(game->GetSettings()->screenWidth + offset - 1, 0);
  	bg.push_back(s2);
  }
```
  Update Parallax  
```cpp
  MoveLayer(backgrounds, 0.005f);
  MoveLayer(middlegrounds, 0.03f);
  MoveLayer(foregrounds, 0.5f);
```

7. ### Draw

   Setelah semuanya selesai jangan lupa menuliskan kode di fungsi Draw agar objek-objek yang telah kita buat dapat di Render ketika game berjalan  
     
```cpp
  DrawLayer(backgrounds);
  DrawLayer(middlegrounds);
  DrawLayer(foregrounds);
  sprite->Draw();
  text->Draw();
  for (Cactus* o : cacti) {
  	o->Draw();
  }
```
