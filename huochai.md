import java.awt.Color;//引入颜色类
import java.awt.Component;//引入组件类
import java.awt.Dimension;//引入封装单个对象中组件的宽度和高度（精确到整数）的类
import java.awt.Graphics;//引入图形类
import java.awt.Graphics2D;//引入二维图形类
import java.awt.event.ActionEvent;//引入事件类
import java.awt.event.ActionListener;//引入事件监听器类
import java.awt.image.BufferedImage;//引入缓冲图像类
import java.util.Stack;//引入栈类
import java.util.Timer;//引入计时器类
import java.util.TimerTask;//引入了实现Runnable接口的类，并具有了线程的功能

import javax.swing.JButton;//引入按钮类
import javax.swing.JComboBox;//引入下拉列表框类
import javax.swing.JFrame;//引入主框架主窗口类
import javax.swing.JLabel;//引入标签类
import javax.swing.JOptionPane;//引入选项面板类
import javax.swing.JPanel;//引入面板类

public class Test//定义一个公共的主类Test
{
	public static void main(String[] args)//定义主方法main
	{
		new Test();//调用Test类的构造方法Test()
	}
	
	private static final int MIN_CNT = 20;//定义一个常量，设置为最小火柴棍数量为20
	private static final int MAX_CNT = 50;//定义一个常量，设置为最大火柴棍数量为50
	private static final int MAX_TAKE = 3;//定义一个常量，设置为最大拿火柴数量为3
	private static final int DELAY = 1000;//定义一个常量，设置为延迟时间为1000
	private JFrame mainFrame;//定义主框架变量
	private MyPaintPanel[] paints;//定义我自己画的面板数组变量
	private JButton userBtn;//定义我的按钮属性
	private JComboBox userNum;//定义我的下拉列表
	private JButton compBtn;//定义电脑按钮属性
	private JLabel compTxt;//定义电脑标签
	private Timer timer;//定义计时器的变量
	
	private Test()//定义一个私有的构造方法，来完成游戏的初始化动作
	{
		mainFrame = new JFrame();//实例化一个主窗口
		mainFrame.setResizable(false);//设置窗体大小不可调整
		mainFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);//设置默认的关闭操作为关闭窗口
		
		JPanel mainPanel = new JPanel();//实例化一个主面板对象
		mainPanel.setLayout(null);//设置布局为空，即没有任何布局
		mainPanel.setPreferredSize(new Dimension(600, 400));//将组件的首选大小设置为常量值
		mainFrame.add(mainPanel);//将主面板添加到框架（窗口）中
		JLabel lab11 = new JLabel("电脑");//创建并设置标签按钮的内容为电脑
		lab11.setHorizontalAlignment(JLabel.CENTER);//设置标签内容沿 X 轴的对齐方式
		lab11.setBounds(0, 10, 200, 20);//设置该标签显示位置为坐标（0,10），宽200，高20
		mainPanel.add(lab11);//将标签加入主面板

		JButton btn11 = new JButton("Reset");//定义一个重新来的按钮
		btn11.addActionListener(new ActionListener()//为“Reset”添加行为事件监听器，采用匿名类的方式
		{
			@Override
			public void actionPerformed(ActionEvent e)//实现监听器中的方法
			{
				init();//当单击此按钮时，调用该初始化方法
			}
		});
		btn11.setBounds(250, 10, 100, 20);//设置重置按钮的位置，左上角坐标为（250，10），大小为宽100，高20
		mainPanel.add(btn11);//重来标签添加到主面板上

		JLabel lab12 = new JLabel("用户");//定义用户标签
		lab12.setHorizontalAlignment(JLabel.CENTER);//设置用户水平方式为居中
		lab12.setBounds(400, 10, 200, 20);//设置用户标签的位置，左上角坐标为（400,10），大小为宽200，高20
		mainPanel.add(lab12);//将用户标签添加到主面板上

		compBtn = new JButton("Computer First");//定义一个电脑首先拿的按钮
		compBtn.addActionListener(new ActionListener()//为电脑开始按钮定义行为事件监听器
		{
			@Override
			public void actionPerformed(ActionEvent e)//行为监听器中的默认方法
			{
				compTake();//当单击电脑按钮时，调用电脑拿方法
			}
		});
		compBtn.setBounds(10, 40, 180, 20);//设置电脑按钮的位置，左上角坐标为（10，40），大小为宽180，高20
		mainPanel.add(compBtn);//按钮添加到主面板上

		compTxt = new JLabel();//定义一个空标签
		compTxt.setBounds(10, 70, 400, 20);//设置空标签的位置，左上角坐标为（10，70），大小为宽400，高20
		mainPanel.add(compTxt);//将空标签加到主面板上

		userBtn = new JButton("Take");//定义用户拿按钮
		userBtn.addActionListener(new ActionListener()//为用户按钮定义行为事件监听器
		{
			@Override
			public void actionPerformed(ActionEvent e)
			{
				userTake();//当单击用户拿按钮时，调用用户拿方法
			}
		});
		userBtn.setBounds(400, 40, 90, 20);//设置用户拿按钮的位置，左上角的坐标为（400,40），大小为宽90，高20
		mainPanel.add(userBtn);//用户按钮添加到主面板上

		userNum = new JComboBox();//定义一个下拉列表框
		for (int i = 0; i < MAX_TAKE; i++)//定义一个拿最大的循环
		{
			userNum.addItem(i + 1);//通过循环给列表框逐个添加选项
		}
		userNum.setBounds(495, 40, 90, 20);//设置下拉列表框中的位置，左上角的坐标为（495,40），大小为宽90，高20
		mainPanel.add(userNum);//将列表框添加到主面板上

		paints = new MyPaintPanel[3];//定义我画的面板
		for (int i = 0; i < 3; i++)//用3次循环来画出三个面板
		{
			paints[i] = new MyPaintPanel();//分别创建3个面板，并调用构造方法初始化三个面板
			paints[i].setBounds(5 + 200 * i, 110, 190, 285);//设置三个面板的位置和坐标，间距为5
			mainPanel.add(paints[i]);//将三个面板分别添加到主面板上
		}
		mainFrame.pack();//调整此窗口的大小，以适合其子组件的首选大小和布局。
		mainFrame.setVisible(true);//设置当前主窗口的可见性
		init();//调用初始化方法
	}

	private void init()//定义初始化的方法
	{
		if (timer != null)//如果计时器不为空的话就执行下面的语句
		{
			timer.cancel();//快速终止计时器的任务执行线程
		}
		timer = new Timer();//实例化一个计时器的对象
		compTxt.setText("");//设置空标签的文本为空
		compBtn.setEnabled(true);//设置是否启用电脑按钮组件，即电脑组件可用
		userBtn.setEnabled(true);//设置是否启用用户按钮组件，即用户组件可用
		paints[0].reset();//调用重置方法，来重新定义第一块面板
		paints[1].reset();//调用重置方法，来重新定义第二块面板
		paints[2].reset();//调用重置方法，来重新定义第三块面板
		int num = MIN_CNT + (int) ((MAX_CNT + 1 - MIN_CNT) * Math.random());//随机产生一个大于20小于50的数
		for (int i = 0; i < num; i++)//分别用for循环添加面板
		{
			paints[1].add(i + 1);//分别将产生的随机数添加到第二块面板
		}
	}

	private void userTake()//定义一个用户拿火柴棒的方法
	{
		compBtn.setEnabled(false);//设置按钮不可用
		int takeCnt = userNum.getSelectedIndex() + 1;//定义用户拿得火柴棍的数量
		int rel = paints[1].getCnt();//将自定义的第二个面板中的火柴棍的数量赋给rel
		if (takeCnt > rel)//如果用户拿火柴棍的数量比面板中的数量大的话，执行下面的语句
		{
			JOptionPane.showMessageDialog(mainFrame, "There is only " + rel
					+ " matches.");//用标准对话框类调用显示”There is only    machines“的对话框
			return;
		}
		for (int i = 0; i < takeCnt; i++)//定义一个将第二块面板中的火柴棍的数量移动到用户面板的循环
		{
			int index = paints[1].remove();//将第二个面板要移除的火柴棍的数量给index变量
			paints[2].add(index);//将index的值添加到用户拿的面板上
		}
		if (takeCnt == rel)//如果用户拿的火柴棍的数量和第二个相等执行下面的语句
		{
			JOptionPane.showMessageDialog(mainFrame, "You are the winner.");//显示”你胜利的对话框“
			init();//并重新初始化
		}
		else//如果不相等的话，执行下面的语句，
		{
			compTake();//让电脑接着拿火柴棍
		}
	}

	private void compTake()//定义电脑拿的方法
	{
		compBtn.setEnabled(false);//设置电脑按钮的当前状态为不可用
		userBtn.setEnabled(false);//设置用户按钮的当前状态为不可用
		MyTask task = new MyTask();//实例化一个MyTask的对象
		timer.schedule(task, DELAY);// 执行线程，安排在指定延迟1秒后执行指定的任务
	}

	private class MyTask extends TimerTask  //定义一个继承了实现Runnable接口的类使自己也具有线程功能
	{
		@Override
		public void run()//因为TimerTask实现了Runnable接口，重写父类的run方法
		{
			int rel = paints[1].getCnt();//将火柴棍面板中的火柴的数量赋给rel
			int takeCnt = 0;//设置当前拿火柴棍的数量为0
			if (rel <= MAX_TAKE)//如果当前火柴棍的数量比最大拿火柴的数量小的话，执行下面的语句
			{
				takeCnt = rel;//将火柴棍的数量赋给电脑要拿的数量
			}
			else if (rel % (MAX_TAKE + 1) > 0)//如果面板中的火柴棍的数量对4取余大于0的话执行下面的语句
			{
				takeCnt = rel % (MAX_TAKE + 1);//将面板中的火柴的数量对4取余之后赋给电脑要拿的火柴的数量
			}
			else   //否则面板中的火柴棍的数量对4取余小于或等于0的话执行下面的语句
			{
				takeCnt = 1 + (int) (MAX_TAKE * Math.random());//随机产生一个小于4的数，给电脑要拿的数量
			}
			for (int i = 0; i < takeCnt; i++)
			{
				int index = paints[1].remove();//将火柴面板中的火柴移除并赋给index
				paints[0].add(index);//将移除的火柴添加到电脑拿的面板上
			}
			compTxt.setText("Computer takes " + takeCnt + " matches this time.");//设置电脑显示拿火柴的数量
			if (takeCnt == rel)//如果和电脑那的数量和面板中的数量相等执行下面的语句
			{
				JOptionPane.showMessageDialog(mainFrame,   //显示信息对话框提示电脑胜利了！
						"Computer is the winner.");
				init();//并重新初始化
			}
			userBtn.setEnabled(true);//设置用户按钮重新可用
		}
	}
	@SuppressWarnings("serial")
	private class MyPaintPanel extends Component //定义一个自定义的面板类并继承了组件类
	{
		private BufferedImage bimg;//创建一个BufferedImage的变量bimg
		private Stack<Integer> data;//定义一个私有的栈类型的变量data

		private MyPaintPanel()//构造我的初始面板的方法
		{
			bimg = new BufferedImage(190, 285, BufferedImage.TYPE_3BYTE_BGR);//表示一个具有 8 位 RGB 颜色分量的图像，
			Graphics2D g2 = bimg.createGraphics();//创建一个 Graphics2D，并将它绘制到此 BufferedImage 中
			g2.setColor(Color.WHITE);//设置要画的颜色，即面板的颜色
			g2.fillRect(0, 0, 190, 285);//在坐标（0,0）处画一个大小为宽190，高为285的白色矩形区域
			g2.dispose();//释放此图形的上下文以及它使用的所有系统资源。调用 dispose 之后，就不能再使用 Graphics 对象。
			data = new Stack<Integer>();//在此面板中实例化一个具体的栈的对象
		}

		public void paint(Graphics g)//定义画图形的默认方法
		{
			g.drawImage(bimg, 0, 0, null);//从坐标（0,0）出开始画图片
			g.dispose();//释放此图形的上下文以及它使用的所有系统资源。调用 dispose 之后，就不能再使用 Graphics 对象。
		}

		private void add(int num)//添加火柴棍的方法
		{
			Graphics2D g2 = bimg.createGraphics();//创建一个 Graphics2D，并将它绘制到此 BufferedImage 中
			int loc = data.size();//将当前栈的大小赋给变量loc
			//System.out.println("当前栈为"+data.size());
			int offX = loc % 3 * 65;//动态的坐标
			int offY = loc / 3 * 15;//动态的坐标
			g2.setColor(Color.YELLOW);//设置火柴棒的颜色为黄色
			g2.fillRect(offX + 8, offY + 5, 50, 6);//填充火柴棒的颜色区域
			g2.setColor(Color.GREEN);//设置火柴棒头的颜色为绿色
			g2.fillArc(offX + 50, offY + 3, 10, 10, 0, 360);//设置火柴棒头的坐标，大小为宽和高为10的360度的弧
			g2.setColor(Color.BLACK);//设置当前火柴棒的根数的颜色
			g2.drawString(String.valueOf(num), offX, offY
					+ g2.getFont().getSize());//画出数字
			data.push(num);//把数字1压入堆栈顶部
			repaint();//重画
			g2.dispose();//释放此图形的上下文以及它使用的所有系统资源。调用 dispose 之后，就不能再使用 Graphics对象
		}

		private int remove()//定义一个移除火柴棍的方法
		{
			Graphics2D g2 = bimg.createGraphics();//创建一个 Graphics2D，并将它绘制到此 BufferedImage 中
			int loc = data.size() - 1;//将当前栈的大小即火柴棍的数量减1
			//System.out.println("当前栈为"+data.size());
			int offX = loc % 3 * 65;//动态的获取坐标
			int offY = loc / 3 * 15;//动态的获取坐标
			g2.setColor(Color.WHITE);//设置移除填充颜色为白色
			g2.fillRect(offX, offY, 60, 15);//画出移除火柴棍的位置
			g2.dispose();//释放此图形的上下文以及它使用的所有系统资源。调用 dispose 之后，就不能再使用 Graphics 对象
			repaint();//调用重新画的方法
			return data.pop();//移除堆栈顶部的对象，并作为此函数的值返回该对象。
		}

		private void reset()//定义一个重置的方法
		{
			data.clear();//清楚该面板栈中的内容
			Graphics2D g2 = bimg.createGraphics();//创建一个 Graphics2D，并将它绘制到此 BufferedImage 中
			g2.setColor(Color.WHITE);//设置颜色为白色
			g2.fillRect(0, 0, 190, 285);//在坐标为（0,0）处填充大小为190,285的矩形区域
			g2.dispose();//释放此图形的上下文以及它使用的所有系统资源。调用 dispose 之后，就不能再使用 Graphics 对象。 
			repaint();//重绘此组件。 
		}
		private int getCnt()//定义一个返回第二个面板火柴棍的数量
		{
			return data.size();//返回当前栈中的火柴棍数量
		}
	}
}
