import discord
from PIL import Image, ImageFont, ImageDraw
from discord.ext import commands
import random
from datetime import datetime
import time
import asyncio
from discord.utils import get

client = commands.Bot(command_prefix='-')
client.remove_command("help")
users = []
blacklist = []
blcklistname = []

#invite link:
#https://discord.com/api/oauth2/authorize?client_id=829583489170931764&permissions=8&scope=bot%20applications.commands

clog = open('command_log.txt', 'w')
blog = open('log_blacklist.txt', 'w')
olog = open('log_ovi.txt', 'w')


@client.event
async def on_ready():
    time = datetime.now()
    await client.change_presence(activity=discord.Game('WoodCraft'))
    print('The bot is ready! • %s' % (time))


@client.command(name="info")
async def suggest(ctx):
    embed = discord.Embed(
        title="Help részleg",
        description=
        "*-suggest suggest: <javaslat>* **»** ezzel a paranccsal javaslatot tudsz küldeni, melyre mások szavazni tudnak\n",
        color=0x114d00)
    await ctx.send(embed=embed)


@client.command(name="staffinfo")
@commands.has_permissions(kick_members=True)
async def suggest(ctx):
    embed = discord.Embed(
        title="Staff help részleg",
        description=
        "**-users**\n**-blacklist**\n**-addblacklist @név**\n**-removeblacklist @név**\n**-ovi @user**-n**/delovi @user**",
        color=0x114d00)
    await ctx.author.send(embed=embed)


@client.command(name="suggest")
async def suggest(ctx, *, suggest):

    if ctx.author.id not in blacklist:

        suggest_channel = client.get_channel(832550790836125746)
        number = random.randint(1, 100000000)
        time = datetime.now()

        #await ctx.channel.purge(limit = 1)

        myEmbed = discord.Embed(title=f'{ctx.author.name}',
                                description='',
                                color=0xc2c2a3)
        myEmbed.set_footer(
            text=
            "A bot használatáról Kornel0706-nál érdeklődj • Javaslat ID: %s • %s "
            % (number, time))
        myEmbed.add_field(name="Új javaslat", value=suggest, inline=True)
        myEmbed.set_thumbnail(url=ctx.author.avatar_url)

        message = await suggest_channel.send(embed=myEmbed)
        await ctx.author.send(embed=myEmbed)

        await message.add_reaction('👍')
        await message.add_reaction('👎')
        print(f'{ctx.author.name} javaslatot küldött!')
        if ctx.author.name not in users:
            users.append(ctx.author.name)

        return
    else:
        await ctx.channel.purge(limit=1)
        await ctx.author.send(
            "A javaslatodat nem sikerült elküldeni, mivel a fekete listán rajta vagy!"
        )
        print(f'{ctx.author.name} nem tudott javaslatot küldeni!')


@client.command(name="users")
@commands.has_permissions(kick_members=True)
async def users(ctx):
    await ctx.author.send(
        "A legutolsó restart óta, eddig ők használták a javaslat rendszert: %s"
        % (users))
    await ctx.channel.purge(limit=1)


@client.command(name="addblacklist")
@commands.has_permissions(kick_members=True)
async def suggest(ctx, member: discord.Member):
    if member.id not in blacklist:
        blacklist.append(member.id)
        blcklistname.append(member.name)
    await ctx.channel.purge(limit=1)
    await ctx.author.send(
        f'**{member.name}** sikeresen hozzá adva a fekete listához!')
    print(f'{ctx.author.name} hozzáadta {ctx.member.name}-t a blacklisthez!')


@client.command(name="removeblacklist")
@commands.has_permissions(kick_members=True)
async def suggest(ctx, member: discord.Member):
    if member.id in blacklist:
        blacklist.remove(member.id)
        blcklistname.remove(member.name)
    await ctx.channel.purge(limit=1)
    await ctx.author.send(
        f'**{member.name}** sikeresen eltávolítva a fekete listáról!')
    print(
        f'{ctx.author.name} eltávolította {ctx.member.name}-t a blacklistről!')


@client.command(name="blacklist")
@commands.has_permissions(kick_members=True)
async def suggest(ctx):
    await ctx.author.send("A fekete listára kerültek: %s" % (blcklistname))
    await ctx.channel.purge(limit=1)
    print(
        f'{ctx.author.name} megnézte, hogy kik vannak hozzáadva a  blacklisthez!'
    )


@client.command(name="clear")
@commands.has_permissions(kick_members=True)
async def clear(ctx, amount=2):
    if commands.has_permissions(manage_messages=True):
        await ctx.channel.purge(limit=amount + 1)
        return
    print(f'{ctx.author.name} törölt {amount} üzenetet!')


@client.command(name="ovi")
@commands.has_permissions(kick_members=True)
async def mute(ctx, member: discord.Member):
    muted_role = ctx.guild.get_role(730670395039940719)

    await member.add_roles(muted_role)
    await ctx.author.send(member.mention + " el lett némítva!")
    print(f'{ctx.author.name} elküldte {ctx.member.name}-t az oviba!')


@client.command(name="delovi")
@commands.has_permissions(kick_members=True)
async def mute(ctx, member: discord.Member):
    muted_role = ctx.guild.get_role(730670395039940719)

    await member.remove_roles(muted_role)

    await ctx.author.send(member.mention + " némítása fel lett oldva!")
    print(f'{ctx.author.name} iskolába engedte {ctx.member.name}-t!')


@client.command(name="kick")
@commands.has_permissions(kick_members=True)
async def kick(ctx, member: discord.Member, *, reason='*Nem lett megadva*'):

    await member.kick(reason=reason)

    await ctx.author.send(
        f'{member} ki lett rúgva, ennek oka: %s | {ctx.author.name}' % (reason)
    )
    print(f'{ctx.author.name} kickelte {ctx.member.name}-t!')


clog.close()
blog.close()
olog.close()

token = "ODI5NTgzNDg5MTcwOTMxNzY0.YG6P1A.uxqCkl_3wjxsdkC1N6kKjYvUhaY"
client.run(token)
