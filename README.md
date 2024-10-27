# **Tutorial Pembuatan Game Dino**

## **Tugas Pemrograman Gim A 2024**

Kelompok 8 \- Mewing Studio

**Anggota Kelompok**:

- Faiqurrijal Tamhidy 	225150200111041  
- Muhammad Fahly Fariza 	225150200111042
- Jeyhan Farrel Alfarisqi 	225150207111082  
- Mochamad Bintang Tegar 	225150201111052

[Link Github](https://github.com/qurreee/PemrogramanGame.DinoAyam)


## **Langkah pembuatan**

1. ### Persiapan File

Kita akan menyiapkan 3 File dengan 1 File ScreenManager dan 2 Screen. File yang Menjadi ScreenManager akan kita beri nama `DinoMain.cpp` dan 2 Screen yang kita butuhkan adalah `DinoGUI.cpp` untuk mengatur tampilan Main Menu dan `DinoDino.cpp` untuk mengatur Ingame utama. Kita juga akan membuat Class `Cactus` tersendiri untuk mempermudah rendering dan update banyak objek cactus sekaligus.

gambar

2. ### Pembuatan GUI Main menu

Untuk GUI Main Menu yang kami gunakan, sebagian Besar masih mirip dengan File Tutorial yang diberikan oleh Dosen. GUI yang dibuat didalam File `DinoGUI.cpp` ini akan inisialisasi 1 Text untuk title dan 2 buah sprite Button.  Jangan lupa juga untuk menambahkan inputManager sehingga kita dapat melakukan aksi yang berkaitan. 

```cpp
void Engine::DinoGUI::Init()
{
	// Create a Texture
	Texture* texture = new Texture("buttons.png");

	// Create Sprites
	Sprite* playSprite = (new Sprite(texture, game->GetDefaultSpriteShader(), game->GetDefaultQuad()))
		->SetNumXFrames(6)->SetNumYFrames(1)->AddAnimation("normal", 5, 5)->AddAnimation("hover", 3, 4)
		->AddAnimation("press", 3, 4)->SetAnimationDuration(400);

	Sprite* exitSprite = (new Sprite(texture, game->GetDefaultSpriteShader(), game->GetDefaultQuad()))
		->SetNumXFrames(6)->SetNumYFrames(1)->AddAnimation("normal", 2, 2)->AddAnimation("hover", 0, 1)
		->AddAnimation("press", 0, 1)->SetAnimationDuration(400);

	//Create Buttons
	Button* playButton = new Button(playSprite, "play");
	playButton->SetPosition((game->GetSettings()->screenWidth / 2) - (playSprite->GetScaleWidth() / 2),
		400);
	buttons.push_back(playButton);

	Button* exitButton = new Button(exitSprite, "exit");
	exitButton->SetPosition((game->GetSettings()->screenWidth / 2) - (exitSprite->GetScaleWidth() / 2),
		250);
	buttons.push_back(exitButton);

	// Set play button into active button
	currentButtonIndex = 0;
	buttons[currentButtonIndex]->SetButtonState(Engine::ButtonState::HOVER);

	// Create Text
	text = (new Text("8-bit Arcade In.ttf", 100, game->GetDefaultTextShader()))
		->SetText("Dino Game")->SetPosition(game->GetSettings()->screenWidth * 0.5f-200, game->GetSettings()->screenHeight - 100.0f)->SetColor(235, 229, 52);

	// Add input mappings
	game->GetInputManager()->AddInputMapping("next", SDLK_DOWN)
		->AddInputMapping("prev", SDLK_UP)
		->AddInputMapping("press", SDLK_RETURN);

}
```
Ketika Tombol Play ditekan, kita akan memanggil Instance ScreenManager dan melakukan `SetCurrentScreen(“ingame”)` sehingga Screen yang ditampilkan berpindah ke Screen Ingame.

Di bagian fungsi Update, kita menuliskan algoritma yang dapat alternating antara kedua Button yang ada, ketika tombol next (down arrow) maupun ketika tombol prev (up arrow) ditekan, sehingga salah satunya dapat play animation ketika terpilih.

```cpp
void Engine::DinoGUI::Update()
{
	// Set background
	game->SetBackgroundColor(52, 155, 235);

	if (game->GetInputManager()->IsKeyReleased("next")) {
		// Set previous button to normal state
		buttons[currentButtonIndex]->SetButtonState(Engine::ButtonState::NORMAL);
		// Next Button
		currentButtonIndex = (currentButtonIndex < (int)buttons.size() - 1) ? currentButtonIndex + 1 : currentButtonIndex;
		// Set current button to hover state
		buttons[currentButtonIndex]->SetButtonState(Engine::ButtonState::HOVER);
	}

	if (game->GetInputManager()->IsKeyReleased("prev")) {
		// Set previous button to normal state
		buttons[currentButtonIndex]->SetButtonState(Engine::ButtonState::NORMAL);
		// Prev Button
		currentButtonIndex = currentButtonIndex > 0 ? currentButtonIndex - 1 : currentButtonIndex;
		// Set current button to hover state
		buttons[currentButtonIndex]->SetButtonState(Engine::ButtonState::HOVER);
	}

	if (game->GetInputManager()->IsKeyReleased("press")) {
		// Set current button to press state
		Button* b = buttons[currentButtonIndex];
		b->SetButtonState(Engine::ButtonState::PRESS);
		// If play button then go to InGame, exit button then exit
		if ("play" == b->GetButtonName()) {
			ScreenManager::GetInstance(game)->SetCurrentScreen("ingame");
		}
		else if ("exit" == b->GetButtonName()) {
			game->SetState(Engine::State::EXIT);
		}
	}

	// Update All buttons
	for (Button* b : buttons) {
		b->Update(game->GetGameTime());
	}
}
```
3. ### Kelas Cactus.cpp

4. ### Inisialisasi Ingame DinoDino.cpp

Kita akan melakukan inisialisasi untuk Objek `sprite` yaitu Sprite Character utama yang akan kita kontrol ketika Game dijalankan. Setelah itu kita juga akan melakukan for loop untuk inisialisasi objek `cactus` dan menyimpannya di dalam vector `cacti`.

Inisialisasi suatu sprite dimulai dengan menyiapkan Texture yang sesuai, biasa berbentuk *Sprite Sheet*

Gambar

Setelah itu kita akan Memasukkan Texture yang ada ke dalam Objek Sprite, dan mengatur berbagai atribut seperti `XNumFrames` dan `YNumFrames` yang membagi *SpriteSheet* yang kita punya menjadi kolom-kolom yang kemudian kita bisa gunakan ketika `AddAnimation`. Jangan lupa untuk mengatur Skala Sprite dan posisi awal mula Sprite.

```cpp
texture = new Texture("Chicken.png");
//bikin sprite
sprite = new Sprite(texture, game->GetDefaultSpriteShader(), game->GetDefaultQuad());
//bikin animasi sprite
sprite->SetNumXFrames(6)->SetNumYFrames(2)->AddAnimation("jump", 0, 5)->AddAnimation("walk", 6, 9)->SetScale(4)->SetAnimationDuration(50)->SetPosition(300, -10)->PlayAnim("walk");
//bounding box sprite
sprite->SetBoundingBoxSize(sprite->GetScaleWidth() - (16 * sprite->GetScale()), sprite->GetScaleHeight());
``` 

```cpp
//init cactuses
const int numCacti = 3;
for (int i = 0; i < numCacti; ++i) {
	Engine::Texture* cactusT = new Texture("cactus.png");
	cactusS = new Sprite(cactusT, game->GetDefaultSpriteShader(), game->GetDefaultQuad());
	
	Cactus* cactus = new Cactus(cactusS);
	cacti.push_back(cactus);
}	
```

Kita juga harus menambahkan `InputManager` di dalam fungsi Init, di Game ini, kita cukup memiliki 1 jenis input yang kita beri nama “jump” yang dihubungkan dengan tombol Space dan Arrow Up

```cpp
game->GetInputManager()->AddInputMapping("jump", SDLK_SPACE)->AddInputMapping("jump", SDLK_UP);
```

Lalu juga kita akan menambahkan Text Score sehingga kita tahu score saat sedang berada di halaman Ingame.

```cpp
//text score
score = 0;
text = (new Text("8-bit Arcade In.ttf", 100, game->GetDefaultTextShader()))->SetText("score " + std::to_string(score))->SetPosition( 350, game->GetSettings()->screenHeight - 100.0f)->SetColor(0, 0, 0);
```

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
