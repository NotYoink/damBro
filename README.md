# damBro
damBro built version

Installation process:
1. Download Zip
2. Extract Zip
3. Run damBro.exe in AI folder


Belangrijke regels code:

    public void AImove()
    {
        maxScore = -999999;
        int tijdelijkescore;

        int[][] bitPieces = new int[10][];
        for (int i = 0; i < 10; i++)
        {
            bitPieces[i] = new int[10];
        }
        bitPieces = bord.BitBoard(bord.pieces);
        List<int[][]> startmoves = new List<int[][]>();
        startmoves = GenerateMoves(bitPieces, 1);
        int[][] maxI = startmoves[0];

        foreach (var item in startmoves)
        {
            tijdelijkescore = BerekenZet(false, 1, bord.BitBoard(bord.pieces), item);
            if (tijdelijkescore > maxScore)
            {  
                maxScore = tijdelijkescore;
                maxI = item;
            } 
        }
        //print(maxScore);
        bord.pieces = bord.BoardBit(maxI);
        bord.UpdateBoard();
        bord.EndTurn();
    }

    private int BerekenZet(bool witBeurt, int diepte, int[][] doorgeefbord, int[][] zetBord)
    {
        int speler = (witBeurt) ? -1 : 1;
        doorgeefbord = (int[][])doorgeefbord.Clone();

        if (diepte == maxdepth || GenerateMoves(zetBord, speler).Count == 0)
        {
            return evaluator.CalcBoardValue(zetBord, speler);
        }
        else
        {
            int Score = -999999;
            foreach (var item in GenerateMoves(zetBord, speler*-1))
            {
                int temp = -BerekenZet(WisselSpeler(witBeurt), diepte + 1, zetBord, item);
                Score = Mathf.Max(Score, temp);  
            }
            return Score;
        }
    }
    private List<int[][]> GenerateMoves(int[][] checkPos, int speler)  
    {
        List<int[][]> mogelijkeZet = new List<int[][]>();
        List<int[][]> nieuwbordList = new List<int[][]>();
        List<int> slagList = new List<int>();

        for (int j = 0; j < 10; j++)
        {
            for (int i = 0; i < 10; i++)
            {
                if (checkPos[i][j] != 0)
                {
                    int p = checkPos[i][j];
                    if (p > 0 && speler > 0)
                    {
                        if (p != 2 && p != -2)
                        {
                            NormalMove(checkPos, ref nieuwbordList, ref slagList, i, j, 0, true, speler);
                        }
                        else if (p == 2 || p == -2)
                        {
                            DamMove(checkPos, ref nieuwbordList, ref slagList, i, j, 0, true, speler);
                        }
                    }
                    if(p < 0 && speler < 0)
                    {
                        if (p != 2 && p != -2)
                        {
                            NormalMove(checkPos, ref nieuwbordList, ref slagList, i, j, 0, true, speler);
                        }
                        else if (p == 2 || p == -2)
                        {
                            DamMove(checkPos, ref nieuwbordList, ref slagList, i, j, 0, true, speler);
                        }
                    }
                }
            }
        }
        int Grootste = -1;
        for (int a = 0; a < slagList.Count; a++)
        {
            if (slagList[a] > Grootste)
            {
                mogelijkeZet.Clear();
                Grootste = slagList[a];
            }
            if (slagList[a] == Grootste)
                mogelijkeZet.Add(nieuwbordList[a]);
        }
        foreach (var item in mogelijkeZet)
        {
            for (int i = 0; i < 10; i++)
            {
                if (item[i][0] != 0)
                {
                    if (item[i][0] > 0)
                    {
                        item[i][0] = 2;
                    }
                }
                if (item[i][9] != 0)
                {
                    if (item[i][9] < 0)
                    {
                        item[i][9] = -2;
                    }
                }
            }
        }
        return mogelijkeZet;
    }
    private void NormalMove(int[][] huidigePositie, ref List<int[][]> nieuwbordList, ref List<int> slagList, int x, int y, int slagnr, bool magBewegen, int speler)
    {
        for (int i = 0; i < 4; i++)
        {
            int vak1x = x; int vak1y = y; int vak2x = x; int vak2y = y;
            bord.VakCheck(x, y, ref vak1x, ref vak1y, i);
            bord.VakCheck(vak1x, vak1y, ref vak2x, ref vak2y, i);

            bool richtingKlopt = ((i == 0 || i == 3) && speler == -1) || ((i == 1 || i == 2) && speler == 1);
            if (huidigePositie[vak1x][vak1y] == 0 && richtingKlopt && magBewegen)
            {
                int[][] nieuwbord = new int[10][];
                for (int a = 0; a < 10; a++)
                {
                    int[] huidigePositieRij = new int[10];
                    huidigePositieRij = huidigePositie[a];
                    nieuwbord[a] = (int[])huidigePositieRij.Clone();
                }
                nieuwbord[vak1x][vak1y] = nieuwbord[x][y];
                nieuwbord[x][y] = 0;
                if (vak1y == 0 || vak1y == 9)
                {
                    nieuwbord[vak1x][vak1y] *= 2;
                } 
                nieuwbordList.Add(nieuwbord);
                slagList.Add(0);
            }
            if (huidigePositie[vak1x][vak1y] != 0 && huidigePositie[vak1x][vak1y]*speler < 0 && huidigePositie[vak2x][vak2y] == 0)
            {
                int[][] nieuwbord = new int[10][];
                for (int a = 0; a < 10; a++)
                {
                    int[] huidigePositieRij = new int[10];
                    huidigePositieRij = huidigePositie[a];
                    nieuwbord[a] = (int[])huidigePositieRij.Clone();
                }
                nieuwbord[vak2x][vak2y] = nieuwbord[x][y];
                nieuwbord[x][y] = 0;
                nieuwbord[vak1x][vak1y] = 0;
                if (vak2y == 9 && speler == -1)
                    nieuwbord[vak2x][vak2y] *= 2;
                if (vak2y == 0 && speler == 1)
                    nieuwbord[vak2x][vak2y] *= 2;
                nieuwbordList.Add(nieuwbord);
                slagList.Add(slagnr + 1);
                NormalMove(nieuwbord, ref nieuwbordList, ref slagList, vak2x, vak2y, slagnr + 1, false, speler);
            }
        }
    }
    private void DamMove(int[][] huidigePositie, ref List<int[][]> nieuwbordList, ref List<int> slagList, int x, int y, int slagnr, bool magBewegen, int speler)
    {
        for (int i = 0; i < 4; i++)
        {
            bool kanBewegen = true;
            bool heeftGeslagen = false;
            int springsteenx = 0; int springsteeny = 0;
            int vakx = x; int vaky = y;
            while (kanBewegen)
            {
                bord.VakCheck(vakx, vaky, ref vakx, ref vaky, i);
                if (vakx == 9 || vakx == 0 || vaky == 9 || vaky == 0)
                    kanBewegen = false;
                if (heeftGeslagen == false && huidigePositie[vakx][vaky] == 0 && magBewegen)
                {
                    int[][] nieuwbord = new int[10][];
                    for (int a = 0; a < 10; a++)
                    {
                        int[] huidigePositieRij = new int[10];
                        huidigePositieRij = huidigePositie[a];
                        nieuwbord[a] = (int[])huidigePositieRij.Clone();
                    }
                    nieuwbord[vakx][vaky] = nieuwbord[x][y];
                    nieuwbord[x][y] = 0;
                    nieuwbordList.Add(nieuwbord);
                    slagList.Add(0);
                }
                else if (heeftGeslagen == false && huidigePositie[vakx][vaky] != 0 && huidigePositie[vakx][vaky]*speler < 0)
                {
                    heeftGeslagen = true;
                    springsteenx = vakx;
                    springsteeny = vaky;
                }
                else if (heeftGeslagen == true && huidigePositie[vakx][vaky] == 0)
                {
                    int[][] nieuwbord = new int[10][];
                    for (int a = 0; a < 10; a++)
                    {
                        int[] huidigePositieRij = new int[10];
                        huidigePositieRij = huidigePositie[a];
                        nieuwbord[a] = (int[])huidigePositieRij.Clone();
                    }
                    nieuwbord[vakx][vaky] = nieuwbord[x][y];
                    nieuwbord[x][y] = 0;
                    nieuwbord[springsteenx][springsteeny] = 0;
                    nieuwbordList.Add(nieuwbord);
                    slagList.Add(slagnr + 1);
                    DamMove(nieuwbord, ref nieuwbordList, ref slagList, vakx, vaky, slagnr + 1, false, speler);
                }
                else
                    kanBewegen = false;
            }
        }
    }
    public void VakCheck(int x, int y, ref int return_x, ref int return_y, int i)
    {
        if (i == 0 && x != -1 && x <= 8 && y <= 8)
        {
            return_x = x + 1;
            return_y = y + 1;
        }
        if (i == 1 && x != -1 && x <= 8 && y >= 1)
        {
            return_x = x + 1;
            return_y = y - 1;
        }
        if (i == 2 && x != -1 && x >= 1 && y >= 1)
        {
            return_x = x - 1;
            return_y = y - 1;
        }
        if (i == 3 && x != -1 && x >= 1 && y <= 8)
        {
            return_x = x - 1;
            return_y = y + 1;
        }
    }
