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
