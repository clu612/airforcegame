# airforcegame
```python
    import pygame
    import random
   

    pygame.init()
    pygame.mixer.init() 

    width, height = 740, 480
    screen=pygame.display.set_mode((width, height))
    keep_going = True


    score=0
    life=3
    game_over=False


    key_up=key_down=key_left=key_right = False
    player_pos=[130,100] #change the position to list


    player = pygame.image.load("pythonproject elements/player.png")


    background = pygame.image.load("pythonproject elements/sky.jpg")
    background = pygame.transform.scale(background, (width, height))
    cargo = pygame.image.load("pythonproject elements/airballoon.png")


    bullets=[]
    bullet = pygame.image.load("pythonproject elements/bullet.png")


    enemyImg = pygame.image.load("pythonproject elements/enemy1.png")
    enemyImg=pygame.transform.scale(enemyImg, (75, 75)).convert_alpha()  #use the convert_alpha() method after loading so that the image has per pixel transparency.
    enemys=[[640,100]]
    enemySpeed=-0.3
    enemyMaxnumber=5

    explosions=[] # store explosion location and img index [(x,y),i,t] 
    explosion_anim=[] #store img for animation
    BLACK = (0, 0, 0)
    explosion_time=60


    WHITE = (255,255, 255)

    def draw_text(surf, text, size, x, y):
    font = pygame.font.Font(pygame.font.match_font('arial'), size)
    text_surface = font.render(text, True, BLACK)       ## True denotes the font to be anti-aliased 
    text_rect = text_surface.get_rect()
    text_rect.midtop = (x, y)
    surf.blit(text_surface, text_rect)

    for i in range(9):
        filename = 'Explosion0{}.png'.format(i)
        img = pygame.image.load("pythonproject elements/"+ filename).convert()
        img.set_colorkey(BLACK)
        img= pygame.transform.scale(img, (75, 75))
        explosion_anim.append(img)
    
    shooting_sound = pygame.mixer.Sound('pythonproject elements/pew.wav')
    pygame.mixer.music.load('pythonproject elements/BG.ogg')
    pygame.mixer.music.play(-1)

    while keep_going:
            
   
    screen.fill(0)
    

    screen.blit(background,(0,0))
   
    
  
    screen.blit(cargo,(0,30))
    screen.blit(cargo,(0,135))
    screen.blit(cargo,(0,240))
    screen.blit(cargo,(0,345))
    
    #1.6 - draw the screen elements
    

    game_over=life<1 #step9 make game over if life small then 1
    draw_text(screen, "Score: "+str(score), 18, width -100, 10)
    draw_text(screen, "Life: "+str(life), 18, width/2, 10)
    if(game_over):
        draw_text(screen, "Game Over", 50, width/2, height/2 -40)
        


    if(not game_over):
        screen.blit(player, player_pos)
    

    enemy_index=0
    for bulletPos in bullets:
        
        bulletPos[0]=bulletPos[0]+2
        screen.blit(bullet,bulletPos)

        #remove bullet if out the screen
        if bulletPos[0]<-64 or bulletPos[0]>width or bulletPos[1]<-64 or bulletPos[1]>height:
            bullets.pop(enemy_index)  #remove from list
        enemy_index+=1
  

    if(random.randint(1,100)<3 and len(enemys)<enemyMaxnumber):
        #screen.blit(enemy, badguy)
        enemys.append([640, random.randint(50,430)])
        print("enemys length"+str(len(enemys)))
    
    enemy_index=0
    for enemyPos in enemys:               
        enemyPos[0]+=enemySpeed
        if enemyPos[0]<50:
            enemys.pop(enemy_index)
        screen.blit(enemyImg, enemyPos)
        
    # 6 Check for collistions
        enemyRect=pygame.Rect(enemyImg.get_rect())
        enemyRect.left=enemyPos[0]
        enemyRect.top=enemyPos[1]
        bullet_index=0
        for bulletPos in bullets:
            bulletRect=pygame.Rect(bullet.get_rect()) # get rect of bullet image size
            bulletRect.left=bulletPos[0]
            bulletRect.top=bulletPos[1]            
            if bulletRect.colliderect(enemyRect):
                enemys.pop(enemy_index)
                bullets.pop(bullet_index)
                # step7 play explosion in the location of enemy
                explosions.append([enemyPos,0,explosion_time])
                #step9 update score
                score+=1
                                
            bullet_index+=1
        
        #step 9 if enemy touch play also explosion
        if(not game_over):
            playerRect=pygame.Rect(player.get_rect())
            playerRect.left=player_pos[0]
            playerRect.top=player_pos[1]
            if(playerRect.colliderect(enemyRect)):
                explosions.append([player_pos,0,explosion_time])
                enemys.pop(enemy_index)
                life-=1
     
        enemy_index+=1     
  


    for explosion in explosions:
        if(explosion[1]<9):
            screen.blit(explosion_anim[explosion[1]],explosion[0])
            explosion[2]=explosion[2]-1
            if(explosion[2]<0):     
                explosion[1]=explosion[1]+1
                explosion[2]=explosion_time
                
        else:
            explosions.pop(0) # the first one is always first completed         


     

    pygame.display.flip() #faster the .update()
 
    for event in pygame.event.get():
        # check if the event is the X button
        if event.type==pygame.QUIT:
            keep_going = False

        if event.type == pygame.KEYDOWN:
            if event.key==pygame.K_w:
                key_up=True
            elif event.key==pygame.K_a:
                key_left=True
            elif event.key==pygame.K_s:
                key_down=True
            elif event.key==pygame.K_d:
                key_right=True
        if event.type == pygame.KEYUP:
            if event.key==pygame.K_w:
                key_up=False
            elif event.key==pygame.K_a:
                key_left=False
            elif event.key==pygame.K_s:
                key_down=False
            elif event.key==pygame.K_d:
                key_right=False

        if(not game_over):      
            if event.type==pygame.MOUSEBUTTONDOWN or (event.type==pygame.KEYDOWN and event.key==pygame.K_SPACE):
                bullets.append([player_pos[0],player_pos[1]])
                shooting_sound.play()      # step 8 play the sound when shooting
    if(not game_over):           
      if key_up and player_pos[1]>0:
            player_pos[1]-=1
        elif key_down and player_pos[1]<height-30:
            player_pos[1]+=1
        if key_left and player_pos[0]>0:
            player_pos[0]-=1
        elif key_right and player_pos[0]<width-100:
            player_pos[0]+=1


      pygame.quit()
      exit(0) 
```