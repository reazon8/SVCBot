import discord
from discord.ext import commands
import json
import aiohttp

TOKEN = "TON_BOT_TOKEN_ICI"

bot = commands.Bot(command_prefix="/", intents=discord.Intents.all())
bot.user.name = "SVCBot"

# Chargement des scores
try:
    with open("classement.json", "r") as f:
        classement = json.load(f)
except FileNotFoundError:
    classement = {}

def sauvegarder_classement():
    with open("classement.json", "w") as f:
        json.dump(classement, f, indent=4)

async def changer_avatar():
    url = "https://cdn.discordapp.com/attachments/1040794235873542296/1335589170030317608/nzr_A_digital_promotion_art_for_e_sport_online_cup_dd86de13-5238-4d59-a731-e31e67fb683d.png?ex=67a0b7d8&is=679f6658&hm=fd9905e7737c4af7486bc37a4f8449545468d99faf586dc378aba674f8e4c8b3&"
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            if response.status == 200:
                avatar_data = await response.read()
                await bot.user.edit(avatar=avatar_data)
                print("Avatar mis à jour !")

@bot.event
async def on_ready():
    print(f"Connecté en tant que {bot.user}")
    await changer_avatar()

@bot.command()
async def inscription(ctx, equipe: str, *joueurs: discord.Member):
    if equipe in classement:
        await ctx.send(f"L'équipe {equipe} est déjà inscrite !")
        return
    if len(joueurs) != 4:
        await ctx.send("Une équipe doit avoir exactement 4 joueurs !")
        return
    classement[equipe] = {"points": 0, "joueurs": [j.mention for j in joueurs]}
    sauvegarder_classement()
    await ctx.send(f"Équipe {equipe} inscrite avec succès !")

@bot.command()
async def match(ctx, equipe: str, classement_final: int, kills: int):
    if equipe not in classement:
        await ctx.send("Cette équipe n'est pas inscrite !")
        return
    
    points_position = max(10 - (classement_final - 1), 1) if 1 <= classement_final <= 10 else 0
    if kills <= 5:
        bonus_kills = 1
    elif 6 <= kills <= 10:
        bonus_kills = 5
    elif 11 <= kills <= 15:
        bonus_kills = 10
    else:
        bonus_kills = 0
    
    total_points = points_position + bonus_kills
    classement[equipe]["points"] += total_points
    sauvegarder_classement()
    
    await ctx.send(f"{equipe} a gagné {total_points} points ! Total actuel : {classement[equipe]['points']} pts")

@bot.command()
async def classement(ctx):
    classement_sorted = sorted(classement.items(), key=lambda x: x[1]['points'], reverse=True)
    message = "**Classement du tournoi :**\n"
    for i, (equipe, data) in enumerate(classement_sorted, start=1):
        message += f"{i}. {equipe} - {data['points']} points\n"
    await ctx.send(message)

bot.run(TOKEN)
