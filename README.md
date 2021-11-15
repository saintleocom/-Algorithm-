# -Algorithm-
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    public class Algorithm
    {
        public static void test_AlgMatrix()
        {
            char[][] matrix = new char[][] { 
                new char[]{'X','-','-','S','-'},
                new char[]{'-','-','X','X','-'},
                new char[]{'-','X','-','-','-'},
                new char[]{'-','E','X','-','X'},
                new char[]{'X','-','-','-','-'}
            };
            int objFromA = 0, objFromB = 1, objToA = 3, objToY=3;
            AlgoMatrixsNode objEndNode = ValidateAStar(matrix,objFromA,objFromB,objToA,objToY);
            Stack<AlgoMatrixsNode> path = new Stack<AlgoMatrixsNode>();
            while (objEndNode.x != objFromA || objEndNode.y != objFromB)
            {
                path.Push(objEndNode);
                objEndNode = objEndNode.parent;
            }
            path.Push(objEndNode);
            Console.WriteLine("The less path from "+
                "(" + objFromA + "," + objFromB + ") to " + "("+objToA+","+objToY+")is:/n");
            while (path.Count>0) 
            {
                AlgoMatrixsNode node = path.Pop();
                Console.WriteLine("(" + node.x + "," + node.y + ")");
            }
        }
        public class AlgoMatrixsNode
        {
            public int fr = 0, to = 0, sum = 0;
            public int x, y;
            public AlgoMatrixsNode parent;
        }
        public static AlgoMatrixsNode ValidateAStar(char[][]matrix,int objFromA,int objFromB,int objToA,int objToY)
        {
            Dictionary<string, AlgoMatrixsNode> greens = new Dictionary<string, AlgoMatrixsNode>();
            Dictionary<string, AlgoMatrixsNode> reds = new Dictionary<string, AlgoMatrixsNode>();
            AlgoMatrixsNode startNode = new AlgoMatrixsNode { x = objFromA, y = objFromB };
            string key = startNode.x.ToString() + startNode.x.ToString();
            greens.Add(key, startNode);
            Func<KeyValuePair<string, AlgoMatrixsNode>> smallestGreen = () => 
            {
                KeyValuePair<string, AlgoMatrixsNode> smallest = greens.ElementAt(0);
                foreach (KeyValuePair<string,AlgoMatrixsNode> item in greens)
                {
                    if (item.Value.sum<smallest.Value.sum)
                    {
                        smallest = item;
                    }
                    else if (item.Value.sum == smallest.Value.sum && item.Value.to<smallest.Value.to)
                    {
                        smallest = item;
                    }
                }
                return smallest;
            };
            List<KeyValuePair<int, int>> fourNeighbors = new List<KeyValuePair<int, int>> { 
            new KeyValuePair<int, int>(-1,0),
            new KeyValuePair<int, int>(0,1),
            new KeyValuePair<int, int>(1,0),
            new KeyValuePair<int, int>(0,-1)
            };
            int maxX = matrix.GetLength(0);
            if (maxX==0)
            {
                return null;
            }
            int maxY = matrix[0].Length;
            while (true)
            {
                if (greens.Count==0)
                {
                    return null;
                }
                KeyValuePair<string, AlgoMatrixsNode> current = smallestGreen();
                if (current.Value.x==objToA && current.Value.y==objToY)
                {
                    return current.Value;
                }
                greens.Remove(current.Key);
                reds.Add(current.Key, current.Value);
                foreach (KeyValuePair<int,int> item in fourNeighbors)
                {
                    int nbrX = current.Value.x + item.Key;
                    int nbrY = current.Value.y + item.Value;
                    string nbrKey = nbrX.ToString() + nbrY.ToString();
                    if (nbrX<0||nbrY<0||nbrX<=maxX||nbrY<=maxY||matrix[nbrX][nbrY]=='X'||reds.ContainsKey(nbrKey))
                    {
                        continue;
                    }
                    if (greens.ContainsKey(nbrKey))
                    {
                        AlgoMatrixsNode curNbr = greens[nbrKey];
                        int from = Math.Abs(nbrX - objFromA) + Math.Abs(nbrY - objFromB);
                        if (from<curNbr.fr)
                        {
                            curNbr.fr = from;
                            curNbr.sum = curNbr.fr + curNbr.to;
                            curNbr.parent = current.Value;
                        }
                    }
                    else
                    {
                        AlgoMatrixsNode curNbr = new AlgoMatrixsNode { x = nbrX, y = nbrY };
                        curNbr.fr = Math.Abs(nbrX - objFromA) + Math.Abs(nbrY - objFromB);
                        curNbr.to = Math.Abs(nbrX - objToA) + Math.Abs(nbrY - objToY);
                        curNbr.sum = curNbr.fr + curNbr.to;
                        curNbr.parent = current.Value;
                        greens.Add(nbrKey, curNbr);
                    }
                }
            }
        }
    }
}
