FONT = {
'A':[".###.","#...#","#...#","#####","#...#","#...#","#...#"],
'D':["####.","#...#","#...#","#...#","#...#","#...#","####."],
'E':["#####","#....","#....","####.","#....","#....","#####"],
'F':["#####","#....","#....","####.","#....","#....","#...."],
'G':[".####","#....","#....","#.###","#...#","#...#",".####"],
'H':["#...#","#...#","#...#","#####","#...#","#...#","#...#"],
'L':["#....","#....","#....","#....","#....","#....","#####"],
'O':[".###.","#...#","#...#","#...#","#...#","#...#",".###."],
'R':["####.","#...#","#...#","####.","#.#..","#..#.","#...#"],
'S':[".####","#....","#....",".###.","....#","....#","####."],
'T':["#####","..#..","..#..","..#..","..#..","..#..","..#.."],
'W':["#...#","#...#","#...#","#.#.#","#.#.#","##.##","#...#"],
'X':["#...#","#...#",".#.#.","..#..",".#.#.","#...#","#...#"],
'P':["####.","#...#","#...#","####.","#....","#....","#...."],
'N':["#...#","##..#","#.#.#","#..##","#...#","#...#","#...#"],
'Y':["#...#","#...#",".#.#.","..#..","..#..","..#..","..#.."],
'0':[".###.","#...#","#..##","#.#.#","##..#","#...#",".###."],
'1':["..#..",".##..","..#..","..#..","..#..","..#..",".###."],
'-':[".....",".....",".....",".###.",".....",".....","....."],
' ':["....."]*7,
}
def text(s, x, y, px, fill):
    out=[]
    for i,ch in enumerate(s):
        g=FONT[ch]
        for r,row in enumerate(g):
            for c,v in enumerate(row):
                if v=='#':
                    out.append(f'<rect x="{x+i*6*px+c*px}" y="{y+r*px}" width="{px}" height="{px}"/>')
    return f'<g fill="{fill}">'+''.join(out)+'</g>'
def tw(s,px): return len(s)*6*px-px

def sprite(rows, pal, px, ox=0, oy=0):
    out=[]
    for r,row in enumerate(rows):
        for c,v in enumerate(row):
            if v in pal:
                out.append(f'<rect x="{ox+c*px}" y="{oy+r*px}" width="{px}" height="{px}" fill="{pal[v]}"/>')
    return ''.join(out)

FOX_TOP = [
"..........O..O..",
"..........OOOO..",
".........OOOKO..",
".........OOOOOWK",
"OO.......OOOWW..",
"OOO.OOOOOOOO....",
".WOOOOOOOOOO....",
"..WOOWWWWOO.....",
"....OOOOOOO.....",
]
LEGS1 = ["....K.K..K.K...."]
LEGS2 = [".....K.K.K.K...."]
PAL = {'O':'#f28c28','W':'#fff6e8','K':'#2b1d14'}
PX=4

W,H=800,320
G=270  # ground top
svg=[]
svg.append(f'<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 {W} {H}" width="{W}" height="{H}" shape-rendering="crispEdges">')
svg.append('''<style>
.cloud1{animation:drift 40s linear infinite}
.cloud2{animation:drift 60s linear infinite;animation-delay:-25s}
@keyframes drift{from{transform:translateX(0)}to{transform:translateX(-1000px)}}
.runner{animation:run 8s linear infinite}
@keyframes run{from{transform:translateX(-80px)}to{transform:translateX(880px)}}
.jump{animation:jump 8s linear infinite}
@keyframes jump{
 0%,30%{transform:translateY(0);animation-timing-function:ease-out}
 33.75%{transform:translateY(-70px);animation-timing-function:ease-in}
 37.5%,61.25%{transform:translateY(0);animation-timing-function:ease-out}
 65%{transform:translateY(-70px);animation-timing-function:ease-in}
 68.75%,100%{transform:translateY(0)}}
.f1{animation:f1 .3s step-end infinite}
.f2{animation:f2 .3s step-end infinite}
@keyframes f1{0%{opacity:1}50%{opacity:0}}
@keyframes f2{0%{opacity:0}50%{opacity:1}}
.b1{animation:bump1 8s linear infinite}
.b2{animation:bump2 8s linear infinite}
@keyframes bump1{0%,33%{transform:translateY(0)}34.5%{transform:translateY(-8px)}36%,100%{transform:translateY(0)}}
@keyframes bump2{0%,64.25%{transform:translateY(0)}65.75%{transform:translateY(-8px)}67.25%,100%{transform:translateY(0)}}
.c1{animation:pop1 8s linear infinite;opacity:0}
.c2{animation:pop2 8s linear infinite;opacity:0}
@keyframes pop1{0%,33.7%{opacity:0;transform:translateY(0)}34%{opacity:1}42%{opacity:0;transform:translateY(-44px)}100%{opacity:0}}
@keyframes pop2{0%,64.9%{opacity:0;transform:translateY(0)}65.2%{opacity:1}73%{opacity:0;transform:translateY(-44px)}100%{opacity:0}}
.spin{transform-box:fill-box;transform-origin:center;animation:spin 1.2s steps(6) infinite}
@keyframes spin{0%{transform:scaleX(1)}50%{transform:scaleX(.15)}100%{transform:scaleX(1)}}
.blink{animation:blink 1.2s step-end infinite}
@keyframes blink{0%{opacity:1}50%{opacity:0}}
.tw{animation:tw 2s ease-in-out infinite}
@keyframes tw{0%,100%{opacity:.2}50%{opacity:1}}
</style>''')
# sky
svg.append(f'<rect width="{W}" height="{H}" fill="#6ec6ff"/>')
svg.append(f'<rect y="0" width="{W}" height="60" fill="#5bb4f0"/>')
# sparkles
for i,(x,y) in enumerate([(60,40),(720,30),(150,110),(660,120),(40,160)]):
    svg.append(f'<g class="tw" style="animation-delay:{i*0.4}s" fill="#fff"><rect x="{x}" y="{y}" width="4" height="4"/><rect x="{x-4}" y="{y}" width="4" height="4" opacity=".5"/><rect x="{x+4}" y="{y}" width="4" height="4" opacity=".5"/><rect x="{x}" y="{y-4}" width="4" height="4" opacity=".5"/><rect x="{x}" y="{y+4}" width="4" height="4" opacity=".5"/></g>')
# clouds
CLOUD=["...WWWW.....","..WWWWWWW...",".WWWWWWWWWW.","WWWWWWWWWWWW",".SSSSSSSSSS."]
cp={'W':'#ffffff','S':'#d8eefc'}
def clouds(y,xs,cls):
    s=''.join(sprite(CLOUD,cp,6,x,y) for x in xs)
    return f'<g class="{cls}">{s}</g>'
svg.append(clouds(120,[100,520,1100,1520],'cloud1'))
svg.append(clouds(165,[330,900,1330],'cloud2'))
# hills (stepped)
def hill(cx,w,h,col,dark):
    out=[]; step=8
    for i in range(h//step):
        ww=w-i*step*2
        if ww<=0: break
        out.append(f'<rect x="{cx-ww//2}" y="{G-(i+1)*step}" width="{ww}" height="{step}" fill="{col}"/>')
    return ''.join(out)
svg.append(hill(110,200,64,'#3fa34d',''))
svg.append(hill(700,240,80,'#3fa34d',''))
svg.append(hill(430,140,40,'#58c063',''))
# title
T="FAXELLAA"; px=6
tx=(W-tw(T,px))//2
svg.append(text(T,tx+px,22+px,px,'#1d3557'))
svg.append(text(T,tx,22,px,'#ffd23f'))
S="STAGE 01 - HELLO WORLD"; spx=3
sx=(W-tw(S,spx))//2
svg.append(text(S,sx+2,78+2,spx,'#1d3557'))
svg.append(text(S,sx,78,spx,'#ffffff'))
# HUD
svg.append(text("PLAYER 1",16,12,2,'#ffffff'))
P="PRESS START"
svg.append(f'<g class="blink">{text(P,W-16-tw(P,2),12,2,"#ffffff")}</g>')
# floating spinning coins
def coin(x,y,cls='spin',extra=''):
    return (f'<g class="{cls}" {extra}><rect x="{x+4}" y="{y}" width="8" height="20" fill="#ffd23f"/>'
            f'<rect x="{x}" y="{y+4}" width="16" height="12" fill="#ffd23f"/>'
            f'<rect x="{x+6}" y="{y+4}" width="4" height="12" fill="#e0a800"/></g>')
for i,x in enumerate([660,690,720]):
    svg.append(coin(x,200-i*0, extra=f'style="animation-delay:{i*0.2}s"'))
for i,x in enumerate([80,110]):
    svg.append(coin(x,205, extra=f'style="animation-delay:{i*0.2}s"'))
# crystal blocks
def block(x,y):
    return (f'<rect x="{x}" y="{y}" width="32" height="32" fill="#2b1d14"/>'
            f'<rect x="{x+2}" y="{y+2}" width="28" height="28" fill="#8e5cf7"/>'
            f'<rect x="{x+2}" y="{y+2}" width="28" height="4" fill="#b794ff"/>'
            f'<rect x="{x+2}" y="{y+26}" width="28" height="4" fill="#6a3fd1"/>'
            f'<rect x="{x+14}" y="{y+8}" width="4" height="16" fill="#fff"/>'
            f'<rect x="{x+8}" y="{y+14}" width="16" height="4" fill="#fff"/>')
BY=128
for cls_b,cls_c,bx in [('b1','c1',260),('b2','c2',560)]:
    svg.append(f'<g class="{cls_c}">{coin(bx+8,BY-24,"spin")}</g>')
    svg.append(f'<g class="{cls_b}">{block(bx,BY)}</g>')
# ground
svg.append(f'''<defs><pattern id="brick" width="32" height="25" patternUnits="userSpaceOnUse" y="{G+8}">
<rect width="32" height="25" fill="#b5651d"/>
<rect y="0" width="32" height="3" fill="#7a3e10"/>
<rect y="12" width="32" height="3" fill="#7a3e10"/>
<rect x="0" y="0" width="3" height="12" fill="#7a3e10"/>
<rect x="16" y="12" width="3" height="13" fill="#7a3e10"/>
<rect x="4" y="4" width="6" height="3" fill="#d9883a"/>
<rect x="20" y="16" width="6" height="3" fill="#d9883a"/>
</pattern></defs>''')
svg.append(f'<rect x="0" y="{G}" width="{W}" height="8" fill="#58c063"/>')
svg.append(f'<rect x="0" y="{G}" width="{W}" height="3" fill="#8ee59a"/>')
svg.append(f'<rect x="0" y="{G+8}" width="{W}" height="{H-G-8}" fill="url(#brick)"/>')
# fox
fy=G-40
body=sprite(FOX_TOP,PAL,PX,0,fy)
l1=sprite(LEGS1,PAL,PX,0,fy+36)
l2=sprite(LEGS2,PAL,PX,0,fy+36)
svg.append(f'<g class="runner"><g class="jump">{body}<g class="f1">{l1}</g><g class="f2">{l2}</g></g></g>')
svg.append('</svg>')
open('/home/claude/out/assets/pixel-banner.svg','w').write('\n'.join(svg))
print("ok")
